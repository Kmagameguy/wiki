# (Deprecated) System-Wide Loudness Equalization on Pop! OS

???+ warning "Deprecated Guide"
     As of Jan 2022 I have started to experiment with Pipewire & EasyEffects.  The guide below should be perfectly serviceable for anyone using PulseAudio & PulseEffects-Legacy

I've found Pop! OS' default dynamic range to be wayyyyy too large.  Even at ~15% volume dialogue vs. action sequences are like the difference between a whisper and a siren going off in your ears.  We can use PulseEffects to emulate Windows' "Loudness Equalization" feature across the whole system.

## Installation
[PulseEffects](https://www.flathub.org/apps/details/com.github.wwmm.pulseeffects) can be installed on bare metal or via flatpak.  It offers a GUI which can be used to enable all sorts of audio filters for pulseaudio.  For now I've decided on using the flatpak release, but I may switch to a bare metal install since flatpaks can't be set to launch on startup.

1. Install the flatpak:

```
$ sudo flatpak install flathub com.github.wwmm.pulseeffects
```

???+ note PulseEffects and EasyEffects
     _Be sure to install PulseEffects, NOT EasyEffects! EasyEffects requires PipeWire which_
     _we're not interested in dealing with at the moment._

## Configuration
Luckily another [github user has done the hard work](https://github.com/Digitalone1/EasyEffects-Presets) for us and offers a repository with a Loudness Equalization preset.

1. Clone the config files to somewhere useful
1. Copy the LoudnessEqualizerPE.json file to the flatpak's data folder:
    - `~/.var/app/com.github.wwmm.pulseeffects/config/PulseEffects/output/`
1. Run PulseEffects
1. Click the "Presets" button at the top, find the Loudness Equalization preset and load it
1. Turn on the "Start Service at Login" setting in PulseEffects' settings menu
1. Close the app and it will continue to operate in the background

???+ note Other Modifications
     _You can also use PulseEffects to apply compressor, limiter, gate, and other effects to your microphone!_

---

## Assign Shortcuts to Volume Up / Down Steps
For some reason Pop! OS doesn't assign keys to the mute / volume-up / volume-down shortcuts by default.  We can add our own using the Settings > Keyboard > Customize Shortcuts panel.

1. Find the "Sound and Media" subcategory
1. Add shortcuts to the volume keys, e.g.:
    - Volume Down => Super + F2
    - Volume Up   => Super + F3
    - Mute/Unmute => Super + F1
As a bonus, these can then be used in Piper, if you have that set up!

---

## Fix Volume Up / Down Increments
By default, GNOME sets the volume up / down increments to 6%.  This makes it REALLY hard to find a volume level that lands in just the right spot between not-too-quiet and not-too-loud.  Luckily there is a setting we can tweak to set this however we'd like.

### Check Current Increment Value
First, let's see what the volume increment is set to:

```
$ gsettings get org.gnome.settings-daemon.plugins.media-keys volume-step
```

At time of writing, the default returned is "6" (i.e. 6%)
To change the value we can use `gsettings` again, but with the "set" command.

???+ warning About Volume Steps
     _The volume-step setting must be between values of 1 and 20_

For example, to set an increment of 2%:

```
$ gsettings set org.gnome.settings-daemon.plugins.media-keys volume-step 2
```

The change should take effect immediately.  You can check whether the change was accepted by re-running the `gsettings get` command above.

---

## Fix Audio Popping

I've noticed my line-out speakers seem to power down / power up frequently.  The indicator is a punchy popping sound whenever a new sound alert is triggered (it is especially bad whenever I receive a message notification in GChat over Google Chrome).

It looks like you can fix that by turning off power-saving settings for the device.  Notes taken from here:  
<https://askubuntu.com/questions/1230833/annoying-click-popping-sound-on-ubuntu-20-04>

### Check Sound Card's Power Save Mode

```
cat /sys/module/snd_hda_intel/parameters/power_save
```

If it returns a value of `1`, then power saving is enabled.  To temporarily turn this off to test for effectiveness, do this:

```
echo "0" | sudo tee /sys/module/snd_hda_intel/parameters/power_save
```

If this change works then you can persist the configuration like so:

```
echo "options snd_hda_intel power_save=0" | sudo tee -a /etc/modprobe.d/audio_disable_powersave.conf
```

(Optional) You can also do the same for `power_save_controller` by following steps 1, 2, & 3 above, but by replacing `power_save` with `power_save_controller`, AND changing `0` to `N`.

**ALTERNATIVE OPTION**  
Some users have reported that the above fix does not work for them.  As an alternative, the Arch Wiki explains how to disable power saving mode at the pulseaudio level:  
<https://wiki.archlinux.org/title/Power_management#PulseAudio>

- Open `/etc/pulse/default.pa` for editing
- Find the `load-module module-suspend-on-idle` line and comment it out
- Restart PulseAudio like so:

```
systemctl restart --user pulseaudio
```

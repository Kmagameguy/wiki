# Fixing the Gallium OS Audio System

Out of the box Gallium OS' audio drivers and settings work pretty well, all things considered.  My only issue was that the default volume cap was aggressively conservative -- even setting the pulseaudio output to 154% resulted in sounds that were barely louder than a whisper.

Luckily this is pretty easy to fix!

## Adjusting Speaker & Headphone Max Volume Level

The max volume level is defined in a `HiFi.conf` file, which can be edited here:

```
sudo nano /usr/share/alsa/ucm/chtmax98090/HiFi.conf
```

With that file open, look for lines that look like this:

```
[...]

cset "name='Headphone Volume' 10"
cset "name='Speaker Volume' 10"
```

Change those values to something much higher.  In my case I went with:

```
cset "name='Headphone Volume' 25"
cset "name='Speaker Volume' 35"
```

Save the edits and then reboot the system.  Test the audio volume slider -- if the maximum is still too low (or, now, too high), edit the file again and tweak the settings.  There is likely some danger in setting the volume too high, so it's good to find a comfortable maximum and adjust no further than that!
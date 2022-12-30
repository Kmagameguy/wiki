# Setting Up PipeWire with EasyEffects
  
Since starting out with Pop! OS I have encountered two annoying issues with the audio system:  
  
1. Dynamic Range compression (aka Loudness Equalization) is almost required to make the audio listenable
1. Audio latency on my input mic is really bad.  My broadcast audio in video meetings is almost a full second out of sync with my webcam video  
  
The former I had been addressing via [PulseEffects-Legacy][legacy-effects], but that only worsened the audio latency.  PulseEffects didn't like buffer sizes smaller than `4096`, either, despite my desktop being pretty powerful.  
  
After extensive research it sounded like [PipeWire][pipewire] could be a cure-all.  I've only been running it for a little while but I'm happy to report my audio performance does indeed seem much better.  Luckily, Pop!'s packages include PipeWire (albeit not the latest release) so it was pretty easy to set up.
  
???+ tip
     _Before starting, I would recommend making a [Timeshift][tshift] snapshot.  The first time I tried to set up PipeWire resulted in undetectable input/output devices and Timeshift made undoing the carnage much easier than manually removing and downgrading packages._
  
## Dumping PulseEffects
  
Since PulseEffects is designed for PulseAudio, and superseded by [EasyEffects][ez-effects], it's best to dump it first.  In my case I was using a native package instead of a flatpak so I just removed it with apt:  

	sudo apt remove pulseeffects

!!! tip "Leftover Audio Modules"
    _When removing pulseeffects apt may suggest other audio plugins / modules for removal.  Leave these alone -- EasyEffects can make use of them once we install it later._
  
## Installing PipeWire
  
As mentioned above, PipeWire is included in Pop!'s package manager.  It won't be the latest version, but I think it is better to install the included copy rather than pulling newer versions from a PPA or elsewhere.  In theory the included version will be less likely to break future Pop! OS distro releases.  Of course, I am not doing any audio editing/mixing/streaming so my needs may differ from yours.  If you are doing music production, for instance, it might make sense to seek out newer versions of PipeWire which will undoubtedly contain stability/quality of life improvements.  
  
Anyway, in addition to PipeWire we'll load up some other packages to support GStreamer plugins, JACK, and Bluetooth audio.
  
### PipeWire
  
Install PipeWire core:

	sudo apt install pipewire
  
### PipeWire Add-Ons
  
Install the GStreamer, Bluetooth, and JACK support (curate to your liking):

	sudo apt install gstreamer1.0-pipewire libpipewire-0.3-{0,dev,modules} libspa-0.2-{bluetooth,dev,jack,modules} pipewire{,-{audio-client-libraries,pulse,media-session,bin,tests}}
  
### Reboot
  
Restart to make sure the system picks up the changes:

	systemctl reboot
  
### Confirming PipeWire Installation
  
Once the system starts back up it should automatically be using PipeWire.  You can check by running:

	pactl info | grep '^Server Name'

The output should look something like:

	Server Name: PulseAudio (on PipeWire 0.3.32)

That's pretty much it!  Next we'll add [EasyEffects][ez-effects] so we can do system-wide loudness equalization.
  
## EasyEffects
  
EasyEffects is the PipeWire replacement for the PulseEffects-Legacy project and only works with PipeWire.  It works like a souped up equalizer that can manipulate input/output devices or individual applications via plugins (e.g. compressors, gates, limiters, etc).  
  
### Installing EasyEffects
  
Unfortunately it only seems to come [packaged as a flatpak][flatpak] for debian-based systems so we'll have to install it that way:  

	sudo flatpak install flathub com.github.wwmm.easyeffects

Select "Y" when prompted by the flathub tool to install EasyEffects & its plugins.
  
### Importing the Loudness Equalization Preset
  
We're going to make this really easy by borrowing a preset that's already been
put together by [`Digitalone1` on GitHub][loudness-preset]:

1. Open terminal and navigate to a sensible place to temporarily store Digitalone1's repo
1. Download the repo contents:  
  
		git clone https://github.com/Digitalone1/EasyEffects-Presets.git
  
1. Copy the preset into this EasyEffects directory:  
  
		cp LoudnessEqualizer.json ~/.var/app/com.github.wwmm.easyeffects/config/easyeffects/output/
  
That will pre-load the preset for use in EasyEffects.  Note the filename is important: `LoudnessEqualizer.json` is the file you want.  Once the file has been copied over you can delete the Digitalone1 repo from your system.

### Configuring EasyEffects
  
Setting up loudness equalization from here is simple:  
  
1. Start EasyEffects
1. Make sure the `Output` tab is selected
1. Click the `Presets` dropdown
1. Click the `Speakers` tab
1. Find the LoudnessEqualizer preset entry and click `Load`

Nice, preset is loaded.  Before we can call it job done we'll want to alter some basic quality of life settings:  
  
1. Start EasyEffects if it's not already open
1. Hit the hamburger menu and open `Preferences`
1. Change these settings on the `General` tab:
    - Start Service at Login: ON
    - Shutdown on Window Close: OFF
    - Process All Inputs: OFF
    - (OPTIONAL) Use Dark Theme: Your choice
1. Explore the settings on the `Spectrum` tab.  My config is below:
    - State -> Enabled: ON
    - Shape: Bars
    - Points: 50
    - Height: 240px
    - Line Width: 5px
    - Fill: ON
    - Show Bars Border: ON
    - Rounded Corners: OFF
1. Close the `Preferences` pane
1. Switch the main interface to the `PipeWire` tab
1. Make sure `Output` is selected at the very bottom of this panel
1. Open `General`
1. Unless you have multiple input devices, turn off the Input Device -> Use Default setting and hard assign your microphone to the dropdown menu
1. Open `Presets Autoloading`
1. Select your default speakers AND the LoudnessEqualizer preset, then click the Plus (+) button
    - _This will make sure the loudness equalizer is always applied to your default audio device_
  
Now we are all set!  You can also explore other goodies inside EasyEffects, such as microphone settings.  I have a simple preset configured which boosts the volume a hair and fixes the audio/video desync issue I mentioned at the outset:  
  
1. Open EasyEffects
1. Switch to the `Input` tab
1. Switch to the `Plugins` module
1. Click `Add Plugin -> Loudness`
1. Set FFT Size to `256`
    - _This sets a small buffer to decrease latency_
1. Set Output Volume to +0.5dB
    - _This boosts the mic volume half a decibel.  This was just enough to get my microphone working at a reasonable volume_
  
Now there's nothing left to do but enjoy a usable audio experience!  
  
[legacy-effects]:https://github.com/wwmm/easyeffects/tree/pulseaudio-legacy
[pipewire]:https://pipewire.org/
[ez-effects]:https://github.com/wwmm/easyeffects
[tshift]:https://teejeetech.com/timeshift/
[flatpak]:https://flathub.org/apps/details/com.github.wwmm.easyeffects
[loudness-preset]:https://github.com/Digitalone1/EasyEffects-Presets

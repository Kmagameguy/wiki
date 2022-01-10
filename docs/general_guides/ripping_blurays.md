# Ripping Blu-Rays
Ripping DVDs and Blu-Rays is an arcane art.  There are hundreds of configuration options to choose from that impact everything from filesize, picture & audio quality, down to playability on different devices.  The notes below were taken as I was attempting to backup some home video for storage in Jellyfin.  
  
## MakeMKV
  
MakeMKV is used to rip the Blu-Ray disk.  It does this by wrapping everything inside MKV containers.  MakeMKV is in beta currently and therefore has a built-in (expiring) key.  You will have to reactivate the program with a new beta key roughly every month (or purchase a key).  

### Installation
  
** Build Script **  
  
On linux you will need to build MakeMKV from source.  I have a script for Debian-based systems in my [dotfiles][dots] which will do the heavy lifting for you.  PRs welcome.  The script will build FFMPEG, the MakeMKV-cli, the MakeMKV-gui, and install the build/runtime dependencies via the package manager.  
  
??? note "Manual Build Instructions"
     If you like to do things the DIY way, the MakeMKV forum has a post that details the steps required to reproduce the MakeMKV build:
     <https://forum.makemkv.com/forum/viewtopic?f=3&t=224>

Register the application using the beta key, found here: <https://forum.makemkv.com/forum/viewtopic.php?f=5&t=1053>  
  
If you like the software and use it frequently, I would encourage you to [purchase a license][makemkv-buy].  At time of writing it is a one-time $60 USD fee that supports the development and saves you the trouble of hunting down a new beta key every month.  
  
### Using MakeMKV
  
1. Insert a disc and click the big HDD icon to run a content scan
1. Select the track(s) you want to rip
1. Alternatively, you can rip the entire disc and then use your video player of choice to whittle down the media you want to keep
  
??? note "A word on disc space"
     _Blu-rays, especially the UHD flavor, can store a LOT of data -- at time of writing, up to 100GB on the triple-layer discs.  Be sure you have plenty of drive space available before beginning a rip!_
  
---
  
## HandBrake

### Installation
  
From Flathub:

```
flatpak install flathub fr.handbrake.ghb
``` 
  
### Summary
  
Below is a good preset for high-quality encodes from a Blu-Ray source.

??? note "Concerning H.264 vs. H.265"
     At time of writing, H.264 has more widespread video player support than H.265.  However, H.265 is gaining traction and arguably uses a much better compression scheme.  If you don't need to worry about widespread playback compatibility, and/or if you are concerned about disk space, H.265 may be better for you
  
1. Format: Matroska (mkv)
1. Passthru Common Metadata: true
1. Save As: [Title Dependent].mkv | To: [Title Dependent]

### Dimensions

1. Resolution Limit: 1080p Full HD
1. Maximum Size: 1920 x 1080
1. Anamorphic: Automatic
1. Pixel Aspect: 1:1
1. Scaled Size: [AutoCalc]
1. Optimal Size: True
1. Allow Upscaling: False
1. Display Size: [AutoCalc] | Automatic: True

### Filters

1. ALL : off

### Video

1. Video Encoder: H.264 (x264)
1. Framerate: Same as source (Variable Framerate, 1. Constant Quality)
1. RF: 22 (or 20, depending on whether you want absolute quality)
1. Preset (slider): veryslow
1. Tune: Film
1. Profile: High
1. Level: [AutoCalc]

### Audio

1. DTS-HD Passthrough, 5.1ch (if available; use next best otherwise)

---

### Subtitles (Optional)

1. Delete Track List(s) & Open Track Selection 
1. Selection Behavior: First Track Matching Selected Languages
1. Available Languages (English) => Selected Languages
1. Add Foreign Audio Scan Pass: True
1. Add English subtitle track if default audio is not English: False
1. Add Closed Captions when available: False
1. Burn-In Behavior: None
1. Burn-In for deficient players:
    - DVD Subtitles: False
    - Blu-Ray Subtitles: False

### Extracting the Image File
  
Most (if not all?) blu-rays have PGS Subtitles.  These are image files and therefore cannot be easily extracted and not all media players understand the format.  To improve compatibility we can use Optical Character Recognition (OCR) tools to transcribe the images to a text-based format like .srt:  
  
1. Install `ffmpeg` and link it in you $PATH
1. Check the subtitles type used in the MKV: `ffmpeg -i movie.mkv` (look for something like "Stream #0:2(eng): Subtitle: hdmvpgssubtitle) -- The important bit is the Stream ID, in this case "0:2"
1. Use the Stream ID to extract the subtitles to a .sup file, e.g.: `ffmpeg -i movie.mkv -c copy -map 0:2 sub.sup`
  
This will produce as `sub.sup` file which only contains the subtitle content.  
  
### Installing SubtitleEdit
  
Subtitle Edit is a wonderful little tool which makes, well, editing subtitles, very simple.  It uses the tesseract ML library which does all the heavy OCR lifting.  There's only one problem: it's a .Net app for Windows only 😔️  
  
The good news is it runs pretty well via `mono` so let's get it set up:

1. Install runtime dependencies

        sudo apt install mono-complete libhunspell-dev libmpv-dev tesseract-ocr vlc ffmpeg


1. Download the PORTABLE version of SubtitleEdit: [SE Github Releases][subedit]
    - _It is usually labeled as "SE[VERSION_NUMBER].zip", e.g. SE364.zip_
1. Unpack the zip file and store its contents somewhere useful, such as your home folder
    - Example: `/home/<user>/SubtitleEdit`
1. Open a terminal and launch the app with mono
    - Example: `mono /home/<user>/SubtitleEdit/SubtitleEdit.exe`
  
??? note "Creating a Launcher Entry for SubtitleEdit"
    Opening SubtitleEdit through the terminal is simple enough, but we can also elevate it to first-class-citizenship status by creating a launcher entry for it.  Notes below should work on any GNOME-based desktop environment:

    1. Download a png copy of the SubtitleEdit logo from your search engine of choice and save it in your SubtitleEdit directory
        - Example: `/home/<user>/SubtitleEdit/SubtitleEdit_logo.png`
    1. Create a new `SubtitleEdit.desktop` file in `~/.local/share/applications/`
    1. Copy the snippet below into your new .desktop file (edit the paths to match your system):

            [Desktop Entry]
            Name=SubtitleEdit
            Exec=mono /home/<user>/SubtitleEdit/SubtitleEdit.exe
            Icon=/home/<user>/SubtitleEdit/SubtitleEdit_logo.png
            Type=Application
            StartupNotify=false
            StartupWMClass=SubtitleEdit
            Categories=Utility;Development
        
### Using SubtitleEdit

Launch SubtitleEdit and open the `sub.sup` file we created earlier.  Automatically SubtitleEdit will open the OCR modal for you.  Great.  Here are the settings I'd recommend:

1. OCR Method: Tesseract 4
1. Language: English (or whatever your preferred language may be)
1. Engine Mode: Default, based on what is available
1. Italic: ON
1. Dictionary: English (United States) [en_US] (if you prefer a different language you will need to download a different one from the spell check > Get Dictionaries menu)
1. Fix OCR errors: ON
1. Prompt for unknown words: ON
1. Try to guess unknown words: OFF
1. Auto break paragraph if more than two lines: ON
  
These settings will require you to watch the OCR process and respond to prompts.  This is better than a fully unattended transcription because it gives you the opportunity to fix detection errors right away.  
  
When you're ready, click `Start OCR`.  Respond to the prompts and make edits as needed.  When the OCR finishes you may want to go the extra mile and review the transcription line-by-line.  Italics, special characters, and similar characters often produce errors that are easier to fix when you can see the original PGS image file for the selected line.  
  
1. When done, click `OK`.
1. Make sure the "format" dropdown is set to "SubRip (.srt)"
1. SAVE THE FILE!  
  
??? note "More Useful SubtitleEdit Features"
     SubtitleEdit has a lot of features that help you further tidy-up the auto-transcribed text:  
       
     1. Tools > Fix Common Errors will auto-detect common subtitle issues and you can choose to selectively apply the results
     1. The main dialog will highlight subtitle lines that may be too long to fit on some screens.  You can use "Auto BR" or in some cases the "Split line!" buttons to tidy these long-lines up.
  
??? note "Common Errors on Linux"
     Because SubtitleEdit has to run through a compatibility layer it may not always be smooth sailing.  Here are a few things I've run into and how I've fixed them:  
       
    **Out of Memory**  
    This only happened for one disc and I think it was a consequence of the disc being encoded poorly in the first place (I have 32GB RAM...for context).  I ended up stopping/resuming the OCR at a few points and creating multiple .srt files.  Once the contents of those files were tweaked to my liking I pasted them together into a single file using a text editor.  
       
    **CPU Pinned Beyond 100% Usage**  
    SubtitleEdit does require a lot of resources but it shouldn't lock up the system entirely.  I'm not sure what triggers it but occasionally the tesseract binary forkbombs my system during the OCR process.  Usually this makes the system _mostly_ unresponsive and SubtitleEdit will appear stuck.  To fix:  
      
    1. Open a terminal and run `killall tesseract`
    1. Quickly jump back to SubtitleEdit and click "Stop" to halt the OCR
        - If you get an error message in SubtitleEdit, click ignore
        - If the system resumes and hangs again, repeat this step until you're able to successfully stop the OCR; first kill the tesseract processes then try to hit the "Stop" button
    1. Close out any other running processes (in my case it seems to hang if I have any videos playing or streaming)
    1. In the OCR modal, click on the line above the one where things seemed to get stuck and click "Start" again  
      
    **Special Characters, Accented Characters, and Diacritic Marks Crash SubtitleEdit**  
    Most special characters (e.g. è and other unicode characters) will either produce an error mesage or crash SubtitleEdit.  Avoid them and you can patch them back into the resulting .srt file with a text editor if needed.  
      
    **File Browser Navigation Buttons/Icons Not Visible**  
    When browsing for files, I can never see the navigation button icons next to the "Look in:" dropdown.  The important ones, from left-to-right:  
      
    1. Go back a folder
    1. Go up a folder
    1. Create a new folder
    1. Cycle view type


[dots]:https://github.com/Kmagameguy/dotfiles/blob/main/cron/util/build-makemkv.sh
[makemkv-buy]:https://www.makemkv.com/buy/?__c=1
[subedit]:https://github.com/SubtitleEdit/subtitleedit/releases
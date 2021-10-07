# Ripping Blu-Rays
Ripping DVDs and Blu-Rays is an arcane art.  There are hundreds of configuration options to choose from that impact everything from filesize, picture & audio quality, down to playability on different devices.  The notes below were taken as I was attempting to digitize my Blu-Ray DVD collection for use with PLEX & Jellyfin.

## MakeMKV

MakeMKV is used to decrypt and rip the raw Blu-Ray data to disk.  It does this by wrapping everything inside MKV containers.  MakeMKV is in beta currently and therefore has a built-in (expiring) key.  Re-download the application monthly to refresh the key.

1. Download MakeMKV: <https://www.makemkv.com/download/>
1. Register the application using the beta key, found here: <https://forum.makemkv.com/forum/viewtopic.php?f=5&t=1053>
1. Insert the disc and click the big HDD icon to start scanning for tracks
1. Select the tracks you want to rip (the largest track is usually the movie)
1. Alternatively, you can rip the entire disk and then use VLC to whittle down the media you want to keep; this is easier if you're interested in saving extras like director's commentary tracks, subtitles, and special features	

---

## HandBrake

Below is a good preset for high-quality encodes from a Blu-Ray source.  Still need to investigate H264 vs H265.

### Summary

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

### Converting Subtitles to .SRT
Most (if not all?) blu-ray DVDs ship with PGS Subtitles.  These are image files and therefore cannot be easily extracted.  First rip the bluray to mkv, then do the following:

1. Install `ffmpeg` and link it in PATH
1. Check the subtitles type used in the MKV: `ffmpeg -i movie.mkv` (look for something like "Stream #0:0(eng): Subtitle: hdmvpgssubtitle
1. Extract the subtitles with something like: `ffmpeg -i movie.mkv -c copy -map 0:2 sub.sup`
1. Convert the .sup to .srt online: <https://subtitletools.com/convert-to-srt-online>

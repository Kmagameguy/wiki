# Ripping CDs  
  
Ripping CDs has, perhaps unsurprisingly, fallen out of popularity these days.  Unfortunately the gold standard, Exact Audio Copy (EAC), doesn't exist for GNU/Linux (OK, you _could_ run it via WINE but that's a lot of work).  Luckily there are a couple tools still clinging to life, one of which will be the focus of this guide.  It is called `abcde`.  
  
## A Better CD Encoder  
  
Similar to EAC, ABCDE will make CD ripping simple.  And, yes, that's really the name, short-form for **A** **B**etter **CD** **E**ncoder; yes I think it's dumb too.  Anyway, this tool is great for a few reasons:

1. You can use musicbrainz to automatically tag your audio files
1. You can specify the file structure & naming conventions
1. It supports all the popular file formats (ogg/vorbis, flac, mp3, etc)
1. You can encode multiple formats at once -- e.g. flac for lossless backup; mp3 for files-on-the-go
1. All settings can be stored in a single config file which you can commit to version control or include in system backups  
  
## Installation  
  
`abcde` is included in most package managers so head over to the terminal.  I use all debian-based systems, so for me the installation command is simply:

```
$ sudo apt install abcde -y
```
  
## Configuration  
  
`abcde` has a LOT of configuration options and honestly, I had a hard time finding good documentation for it all.  Below is a sample configuration file that will rip to the .FLAC format.  To use it:

1. Create an `.abcde.conf` file in your `$HOME` directory
1. Copy and paste the contents from the sample below into the conf file and save it
    - _I have included some commented lines which can be used to encode to other formats, such as ogg/vorbis for example_
  
```
# -----------------$HOME/.abcde.conf-----------------
# 
# A sample configuration file to convert cds to 
#       FLAC using abcde version 2.9.3-1
# 
# --------------------------------------------------

# Encode tracks immediately after reading. Saves disk space and may
# improve read quality of scratched/low-quality disks.
# Note: this will slow abcde operation down.
LOWDISK=n

# ----- Music Databases ----- 
# ---------------------------

# Tell abcde which online database to compare metadata with,
# at time of writing I've only had success with 'musicbrainz'
CDDBMETHOD=musicbrainz
GLYRC=glyrc

# ----- ENCODER SELECTIONS -----
# ------------------------------

# Set an encoder for ogg vorbis files.  Choice between:
# 1. oggenc
# 2. vorbize
# OGGENCODERSYNTAX=oggenc

# Set an encoder to use for mp3 files. Just use lame.
# MP3ENCODERSYNTAX=lame 

# Set an encoder to use for FLAC files.  AFAIK flac is the only option.
FLACENCODERSYNTAX=flac

# ----- ENCODER PATHS -----
# -------------------------

# ABCDE needs to know where the encoders live on your system.  If
# they aren't already in your $PATH then you will need to specify
# the full path to the binaries, e.g.: /usr/local/bin/<encoder>

# Ogg Vorbis
# OGGENC=oggenc

# MP3
# LAME=lame

# FLAC
FLAC=flac

# ----- ENCODER SETTINGS -----
# ----------------------------

# Each encoder can be passed a set of flags to control the quality of your rips.
# This file only shows some examples, you may need to do your own research
# to rip files perfectly for your needs

# Ogg Vorbis
# The '-q 6' flags set VBR encoding between 192-224 kbits/s.
# OGGENCOPTS='-q 6'  

# MP3
# The '-V 2' option gives VBR encoding between 170-210 kbits/s.
# LAMEOPTS='-V 2' 

# FLAC
# The '-s -e -V -8' flags will:
# -s | rip silently
# -e | do an exhaustive search
# -V | verify the encoding
# -8 | compression level (max) Overall bitrate is about 880 kbs/s with level 8.
FLACOPTS='-s -e -V -8' 

# ----- CD RIPPING BACKEND -----
# ------------------------------

# ABCDE is really just a frontend/wrapper which can utilize different ripping tools under
# the hood.  You can specify which of these you'd like to use below, such as:
# 1. cdda2wav
# 2. dagrab
# 3. flac
# 4. cdparanoia
# 5. libcidio
# (maybe others)
# I prefer cdparanoia despite it's age / maintenance status
CDROMREADERSYNTAX=cdparanoia
                                     
# Just like the encoder sections above, abcde needs to know the
# location of the ripping program.  If it's not in your $PATH then
# explicitly define the binary location, e.g.: /usr/local/bin/cdparanoia
# You should also pass extra options here, too, as needed:
CDPARANOIA=cdparanoia
CDPARANOIAOPTS="--never-skip=40"
# CDDA2WAVOPTS="-paranoia"

# Give the location of the CD identification program:       
CDDISCID=cd-discid

# Use multiple encoders to speed up ripping
MAXPROCS=2

# ----- OUTPUT SETTINGS -----
# ---------------------------

# Tell abcde where to put its temporary files
WAVOUTPUTDIR="/tmp"

# Tell abcde where to store the final files
OUTPUTDIR="$HOME/Music"

# Tell abcde which of the configured file formats you want to rip to.  You can specify multiple with a comma separated list, e.g "=flac,mp3,ogg".
OUTPUTTYPE="flac"

# Force tracks to use 01, 02 formatting instead of 1, 2
PADTRACKS=y

# Tell abcde how to structure the resulting directory and file name(s) when ripping in BATCH:
# Configuration is defined separately for single-artist and multi-artist (compilation) discs
# Lines below would create this structure for the song "Drained" from the album "Battles" by the band "In Flames":
# In Flames/Battles/01.Drained.flac
OUTPUTFORMAT='${ARTISTFILE}/${ALBUMFILE}/${TRACKNUM}.${TRACKFILE}'
VAOUTPUTFORMAT='Various - ${ALBUMFILE}/${TRACKNUM}.${ARTISTFILE}-${TRACKFILE}'

# Tell abcde how to structure the resulting directory and file name(s) when ripping a SINGLE TRACK:
# Configuration is defined separately for single-artist and multi-artist (compilation) discs
# Lines below would create this structure for an album "Battles" by the band "In Flames":
# In Flames/Battles/01.Drained.flac
ONETRACKOUTPUTFORMAT='${ARTISTFILE}/${ALBUMFILE}/${TRACKNUM}.${TRACKFILE}'
VAONETRACKOUTPUTFORMAT='Various - ${ALBUMFILE}/${TRACKNUM}.${ARTISTFILE}-${TRACKFILE}'

# Tell abcde how to set up a playlist from the newly ripped tracks.
# PLAYLISTFORMAT='${OUTPUT}/${ARTISTFILE} - ${ALBUMFILE}/${ALBUMFILE}.m3u'
# VAPLAYLISTFORMAT='${OUTPUT}/Various - ${ALBUMFILE}/${ALBUMFILE}.m3u'

# Define the default actions that abcde will take.
# 1. cddb        | Get disc metadata from online database
# 2. read        | Read the disc
# 3. getalbumart | Check the online database for suitable album art
# 4. encode      | Convert disc contents to specified file format(s)
# 5. tag         | Add track metadata to encoded files
# 6. move        | Move final files out of temp directory into output destination
# 7. clean       | Perform clean-up 
ACTIONS=cddb,read,getalbumart,encode,tag,move,clean

# ----- FORMATTING HELPERS -----
# ------------------------------

# This function tries to sanitize filenames a bit in case tracks/artists/albums use
# illegal characters. It allows spaces, though, so if you want to strip those too add
# in -e 's/ /_/g' after the first sed command.
mungefilename ()
{
  echo "$@" | sed -e 's/^\.*//' | tr -d ":><|*/\"'?[:cntrl:]"
}
```
  
## Using ABCDE

Now that the configuration is...well...configured, we are ready to rip:

1. Insert a CD into the disc drive (you _do_ have one, don't you?)
1. Fire up your terminal
1. Simply run `abcde`
  
And that's it! `abcde` will read your configuration file and begin checking out your disk.  If you used the
config above there will be a few interactive questions:

1. First, it will try to match the disc with musicbrainz's database; With any luck you'll be able to use the first result
1. Second it will ask if you want to modify any of the metadata info; I usually just pick "no"
1. Third it will ask if the disc should rip in single-artist or multi-artist mode
1. It will also try to fetch album art; You can supply your own link if you'd prefer 
    - _You may need to install `imagemagick` for this step to work, or you can remove "getalbumart" from the abcde "ACTIONS" manifest_  
  
From there `abcde` will print its progress as it rips & encodes your music.  There isn't much more to it!

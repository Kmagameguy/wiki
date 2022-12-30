# How to Install Firefox Directly from Mozilla Builds on Linux  
  
By default, most GNU/Linux distributions package their own Firefox builds for release via apt.  While this is perfectly suitable for most use cases, you often have to wait a few days before new releases are merged into your distribution's repositories.  As a result, you may not always have the latest security patches.  For those who need (or want) immediate updates direct from Mozilla, we can replace the distribution maintainer's copy quite easily.  
  
## Back Up Firefox  
Start by backing up your Firefox bookmarks, history, settings, addons, etc, if you've already started using the copy provided by your distribution's maintainer.  Firefox Sync is end-to-end encrypted which makes for an easy backup & restore process.  Otherwise, you will need to manually export and reimport your customizations.  
  
## Installing Dependencies  
First, have a look at Firefox's required and optional dependencies:  
https://www.mozilla.org/en-US/firefox/system-requirements  
  
Usually these are already installed by your distribution.  
  
## Downloading Firefox  
Download the latest version of Firefox directly from Mozilla's FTP listing here:
https://ftp.mozilla.org/pub/firefox/releases  
  
The directory listing (at time of writing) is like this:  
  
- Firefox Versions
    - platform
        - language  
  
For example: To download the 64-bit, US English, Linux version of Firefox you'd go down this path:
(latest version) > linux-x86_64 > en_US

Then download the `.tar.bz2` AND the `.tar.bz2.asc` files.  
  
## Installing Firefox
Extract the `.tar.bz2` file you just downloaded:
  
```
$ cd $HOME/Downloads
$ tar xjf firefox-*.tar.bz2
```

Then move the extracted folder to the `/opt` directory:

```
$ sudo mv firefox /opt/
```

Then create a symlink to the Firefox binary:

```
$ sudo ln -s /opt/firefox/firefox /usr/local/bin/firefox
```

### Creating a Launcher Shortcut

Next you'll want to create a new shortcut file so system utilities like the launcher or applications menu point to the new, Mozilla-sourced Firefox installation:

```
$ sudo wget https://raw.githubusercontent.com/Kmagameguy/dotfiles/main/desktop_files/firefox.desktop -P /usr/local/share/applications
```

### Purging the Old Firefox
This step may differ depending on how your distribution packages Firefox (flatpak, snap, deb, rpm, etc).  In my case, Pop! OS currently distributes Firefox as an apt package, so removing it is easy:

```
$ sudo apt remove --purge firefox*
```

## Restoring Customizations

Launch Firefox and you'll see it's been reset to a fresh profile.  From here you can either sign in to Firefox sync or manually import your addons, settings, extensions, etc.

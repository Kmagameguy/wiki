# Firefox Profiles
Firefox comes packaged with a profile management utility.  This is useful if you want to maintain completely separate "installations" of firefox for different use cases.  For example, maybe you want a "work" profile and "personal" profile in which neither share any bookmarks, history, downloads, etc.  Firefox profiles allow you to do this, and different profiles can even run concurrently with the help of customer launcher entries.

## Create A New Profile Using Profile Manager
Open terminal and run:

```
$ firefox --profilemanager
```

From the GUI, choose "Create Profile" and give it a name.  Make note of this name as we'll be using it to configure the custom launcher.

## Create the Launcher Entry
Instead of running profile manager to launch the different instances of Firefox, we'll map the new one to a new launcher item.  Do that like so:

```
$ nano ~/.local/share/application/firefox-PROFILENAME.desktop

[Desktop Entry]
Encoding=UTF-8
Name=Firefox PROFILENAME
Exec=firefox -P PROFILENAME -no-remote --class PROFILENAMEProfile
Icon=firefox
Type=Application
StartupNotify=True
StartupWMClass=PROFILENAMEProfile
```

Where "PROFILENAME" should be replaced with the name you gave the profile.
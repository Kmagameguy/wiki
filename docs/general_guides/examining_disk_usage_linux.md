# Examining Disk Usage on Linux Systems

An important part of administering any system is making sure
you're not at your disk space limit.  This is especially true
when running something like a media center or NAS which may
run nightly download or backup jobs.

## The GUI Way

Most linux distributions' file browsing app will also display
free vs. used disk space in a bottom or top bar.  Alternatively
you can always navigate to `/` and right-click somewhere to open
a properties window.  Chances are you'll find what you're looking for.

## The Terminal Way

In other cases it may be advantageous to examing disk usage
from the command line.  The most common use case is during an `ssh`
session where a GUI option isn't viable.  To examine disk usage
in the terminal, do the following:

### Check Overall Disk Usage

```
$ df -h

Filesystem    Size  Used  Avail  Use%  Mounted on
udev          1.9G     0   1.9G    0%  /dev
tmpfs         390M  1.5M   388M    1%  /run
/dev/disk1     28G    5G    21G   23%  /dev/disk1
```

## Finding Large Files & Folders

As disk usage creeps up you may find yourself wanting
to perfom an audit of your system.  In doing so, you
may be able to find & delete big files/folders that
are no longer needed.  To do this, I recommend `ncdu`.

`ncdu` is an ncurses utility that builds a filetree
of your system directories and lets you browse them
in the terminal.  Folders are sorted from the supplied
path by their size, which makes it easy to see what's
taking up space on your disk.

### Installing NCDU

```
sudo apt install ncdu
```

and yer done.

### Using NCDU

```
ncdu <optional-filepath>
```

If no filepath is supplied, ncdu will build a listing
based on the current working directory.  It's really
that simple!


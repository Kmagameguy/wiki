# Ripping Multi-Media Disks
Ripping multimedia to the ISO format is a great way to back up disk media.  If you're not following the blu-ray/CD guides, perhaps you need to back up some old family photos, a linux disk image, or something of that sort.  
  
Typically this task is straightforward enough with the `dd` command, but `ddrescue` is a slightly better option, offering these advanced features:

  - Disk error detection/correction (success may vary)
  - Progress bar
  - a simpler/safer commandline syntax (it's much harder to destroy a live disk with `ddrescue` than with `dd`)  

## Installing DDRESCUE

Open a commandline and enter:

```
$ sudo apt install gddrescue -y
```

## Finding Your Disk Drive

Insert your disk into your drive.  Unless you have multiple drives, yours will typically be in `/dev/sr0`.  You may also be able to use `/dev/cdrom` which will be symlinked to your device on certain GNU/Linux distributions.

## Using DDRESCUE

With the disk in the drive we're ready to start.  The backup is invoked with one command:

```
$ ddrescue [/path/to/disk/drive] [/where/to/save/file.iso]
```

Where `[/path/to/disk/drive]` is the disk device you identified in the section above.

Where `[/where/to/save/file.iso]` is the location to store the ISO backup.

Example:

```
$ ddrescue /dev/sr0 /mnt/backups/my_disk_backup.iso
```

## Verifying the Disk

Use `md5sum` against your original disk & the ISO to verify their integrity.  For example:

```
$ md5sum /dev/sr0
57cyj4119nma268eb5776ccf51yi3197

$ md5sum /path/to/iso/file.iso
57cyj4119nma268eb5776ccf51yi3197
```

If the md5sum values match then your ISO is a perfect copy of your original disk!
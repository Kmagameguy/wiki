# Make Snaps Play Nicely With Home Directory Symlinks

## Overview

As it turns out, Canonical's snap packages are complete trash (queue 
gasps of surprise from no-one).  I was recently forced to install
the snap package manager so that I could use my 2FA app of choice, Authy,
on my desktop.  It worked for a little while until one day it suddenly broke
with absolutely zero helpful error or log information.  
  
After spending some time on the snapcraft forums I found a post from users
who experienced the same problem.  It turns out, snap utterly fails if any
directories in your `home` folder are symlinked to paths on a separate drive.  
  
WHAT.  
  
Here is the post in question:  
<https://forum.snapcraft.io/t/some-snaps-will-only-launch-the-first-time/18509/12>  
  
I don't know if this "ogra" user is a representative of Canonical or not but
their reply is exactly the sort of garbage that drives people away from
linux: "just use bind mounts in your fstab".  OK, sure, that is easy enough
to do _if you understand mounts in the first place_.  Canonical MUST fix
these problems if they want to win the war over dependency-packaged-apps.  The
suggestion by this forum poster is the same sort of nonsense everyone roasted
Steve Jobs for whenever he'd say outlandish things like "you're holding your iPhone wrong".  
  
Anyway, I digress.  Let's look at how I fixed this issue WITHOUT fussing with
bind mounts.  Of course, your mileage may vary.  I am lucky because Authy is
the ONLY snap package on my system, and further, it is a very simple application
that does not require filesystem access on account of it simply being a provider
of 2FA codes.  
  
## Fixing Authy's Filesystem Access

Snaps all seem to come with a `user-dirs.dirs` file which contains a set of paths
the snap scans on launch.  We can simply remove the file or comment out the
contents to get authy working again:  

### Option 1: Delete the File

This is the simplest option but is harder to recover from if a snap still won't work.  
  
1. Open terminal
1. Run `sudo rm ~/snap/authy/current/.config/user-dirs.dirs`
1. Launch authy

### Option 2: Comment-Out the File Contents

This is slightly more work but makes it easier to undo changes.  
  
1. Open terminal
1. Run `sudo nano ~/snap/authy/current/.config/user-dirs.dir`
1. Prefix EACH LINE with a comment character: `#`
1. Save the file with this key sequence: `Ctrl + X`, `Y`, `ENTER`
1. Launch authy
  
And you should be good to go.  You will need to do this for each snap if it
doesn't launch.  So far it seems like Bitwarden and Authy are the most
likely to break if your home directory makes use of symlinks.  
  
The bad news is you will probably have to re-apply this tweak whenever your
snaps receive updates.  The good news is Twilio seems like a fairly lazy company
and rarely (if ever) publishes updates to their Authy snap package.  
  
I really hope Canonical shapes up and fixes these dumb bugs in their package manager.
As it stands, AppImages, Flatpaks, and good old .debs seem to be leaps and bounds
more reliable.

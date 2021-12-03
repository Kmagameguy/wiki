# Run Android Apps Without a Smartphone

## Why Would You Do This?
Frustratingly, access to more and more services are locked behind smartphone apps.
For some apps (ridesharing comes to mind) this makes a lot of sense.  However for others,
(e.g. Amazon Care) this makes a lot less sense.  For me this is a worrying trend as it means:

1. Only those privileged with the ability to afford smartphones can access these services.
This fast becomes a discriminatory practice and is fundamentally anti-consumer
1. It affords the vendor more ability to abuse private info.  By restricting access
to mobile apps, vendors are given easier access to mobile phone data that can then be
slurped out of the device and sold off to advertisers, yuck
1. For the privacy-conscious it makes separating employer concerns from personal ones difficult

Number 3 is what prompted me to write this guide.  My employer is increasingly
requiring users to access services which only provide mobile app access.  My phone isn't
compatible with some of these programs, and, I also think it's an invasion of my personal
privacy/rights for an employer to expect access to my personal device(s).  
  
Enter [android-x86][android-x86] and [VirtualBox][vbox].  No matter what your platform
of choice is (Linux, Windows, macOS), you will be able to use this guide to run an
emulated copy of the Android operating system.  This means you will be able to
install and run Android apps right on your desktop/laptop computer.

???+ note "This seems complicated..."
     _Yes, this is far from the most efficient way to run Android. [BlueStacks][stacks] is
     a more user friendly way to run an Android emulator.  Unfortunately I couldn't get
     BlueStacks 4 working on macOS Monterey, but it's just using VirtualBox under the hood anyway.
     So, I'm taking the DIY approach here since it offers more control_

## Overview
If you're with me so far, here's what we're gonna cover:

1. What to download & why
2. How to configure VirtualBox
3. How to install Android in VirtualBox
4. Android first-time setup
5. Installing an app

I will try to keep this guide as platform-agnostic as possible, but if I'm being honest I ran through
these steps on macOS and I don't have a Windows box around for testing purposes.  As a result some of
the steps may be mac-biased but hopefully they're intuitive enough that you'll be able to figure things
out on your own platform of choice!

## Before We Begin
Before we get started, make sure your system has enough resources to support this project:

- Memory: At least 4GB
- Disk space: At least 12GB
- An internet connection

## Downloading Things
The two things we need to get started are VirtualBox & an Android installer.  VirtualBox is a "Virtual Machine"
manager.  A virtual machine is like putting a computer in a box.  Your computer (the "host") runs VirtualBox,
which then runs the second system (the "guest", in our case, the Android system) on top.  The "guest" is displayed
in a Window just like any other program on your computer.  Very cool.

### VirtualBox
Start by downloading [VirtualBox][vbox] from its homepage.  There should be a big download button you can click
once you've followed that link.  The next page is text-heavy and might be disorienting at first.  Just find the
"VirtualBox Binaries" section (it should be first on the page), then look for a header that looks like
"VirtualBox x.x.xx platform packages".  At time of writing the header reads "VirtualBox 6.1.30 platform packages".  
  
Under that heading should be a bulleted list of links.  Find the one that matches your system:

- Windows hosts = Click this if you are on a Windows computer
- OS X hosts = Click this if you are on a Mac computer
- Linux distributions = Click this if you are on a Linux computer (we assume you know what you're doing)

Clicking the Windows or Mac links will begin the download!  Next we'll grab Android.

### Android
The Android package is referred to as an "image" file and contains a pre-configured set of tools that
automatically (well, mostly!) set up a brand new copy of the Android operating system.  If you've ever
inserted a CD into your computer and installed a program from it, this process is effectively very similar!  
  
We'll download the Android image from the [Android-x86][android-x86] project. On the homepage should be a big
"Download" button.  Click that and you'll be taken to a new page which links to a couple different storage
locations (at time of writing, FOSSHUB and OSDN).  I followed the link to FOSSHUB but it doesn't really matter
which one you pick -- they have the same files and are just a way for the project to make sure their servers
distribute traffic to avoid being overloaded.  
  
Both mirror sites have a list of different files so we need to pick the correct one.  Look for ones that are
labeled as an "ISO" file (.iso).  These are offered in two flavors: 32-bit & 64-bit.  Both will work fine but I
prefer the 64-bit version.  So, putting those together, here's what you should look for:

FOSSHUB:

- Download the file labeled "Android-x86 64-bit ISO file" by clicking its link

OSDN:

- Download the file that looks like this: "android-x86_64-8.1-r6.iso".
    - The "-8.1-r6" part of the filename will change over time.  That simply identifies the file version, so yours may look newer than what's written here
    - Notice the "_64" part of the filename -- that identifies the file as the "64-bit" copy.  The 32-bit will just read "x86-8.1-r6" by contrast

Clicking the link will begin the download!  Wait for VirtualBox's installer and this ISO file to finish
downloading before moving on to the next steps!

## Installing VirtualBox

### Installing the Base Program

1. Run the VirtualBox installer

    ??? note "macOS"
        1. Double click the .dmg
        1. Double click "VirtualBox.pkg" in the pop-up window
        1. Click through the install wizard, keeping all settings at their defaults (enter your password if prompted)
        1. Open System Preferences > Security & Privacy > General
        1. Unlock the panel by clicking the lock icon in the lower-left corner and plugging in your password
        1. Click the "Allow" button next to the snippet of text that is asking for permission to allow the "Oracle VM" extension
        1. Restart your Mac so this extension gets loaded
  
    ??? note "Windows"
        1. Double click the .exe
        1. Click through the install wizard, keeping all settings at their defaults (enter your password if prompted)

### Creating an Empty Virtual Machine

1. Open the VirtualBox program
1. Click on "Tools", then the "New" button (it looks like a very pointy circle)
  
In this menu will be a few options we'll need to change.  Set them up like so:

1. **Name:** This can be anything you want. I chose "Android"
1. **Machine Folder:** Pick a place on your computer for VirtualBox to store its files.  I made a new "VirtualBox VMs" folder inside my documents folder
1. **Type:** Linux
1. **Version:** Linux 2.6 / 3.x / 4.x (64-bit)
    - _Select "32-bit" if you chose to download a 32-bit ISO earlier!_
1. Click "Continue"
1. Set the memory size to something like 4096MB.  You may also go as low as 2048MB if necessary; this depends on what your system has available.  If you're unsure, just avoid pushing the slider into the pink/red zones!
1. Click "Continue"
1. Make sure "Create a virtual hard disk now" is selected
1. Click "Create"
1. On the next screen, set the Hard disk file type to "VDI (VirtualBox Disk Image)"
1. Click "Continue"
1. On the next screen, make sure "Dynamically allocated" is selected
1. Click "Continue"
1. Choose a place for VirtualBox to save the virtual hard disk (this path should be pre-filled for you)
1. Set the virtual hard disk size to 8.00 GB
  
### Configuring the Virtual Machine Startup Options

1. Select your newly created Virtual machine from the VirtualBox sidebar
1. Click the "Settings" button in the top menu bar
1. Open the "Display" tab
1. Change "Graphics Controller" to "VBoxVGA"
1. Ensure "3D Acceleration" is OFF
1. Click "OK"
  
Now your virtual machine is all set!  The next steps will get Android installed.  
  
## Installing Android
  
### Loading the Android ISO

1. Select your Virtual machine from the VirtualBox sidebar
1. Click the "Start" button in the top menu
1. VirtualBox will ask you to insert a virtual optical disk -- Click the folder icon
1. Click the "Add" button
1. Find the Android ISO file you downloaded earlier and open it
1. Click "Choose"
1. Click "Start"

### Creating Disk Partitions & Installing Android

The next steps will require you to use your keyboard (arrow keys and the enter key, primarily)

1. When prompted, use your down arrow key to highlight the "Installation - Install Android-x86 to harddisk" option
1. Press the Enter key (return on macOS)
1. Highlight "Create/Modify partitions" with the arrow keys
1. Press "Enter"
1. Highlight "NO" at the "Do you want to use GPT?" screen using the arrow keys
1. Press "Enter"
1. Use the arrow keys to highlight the "New" option at the bottom of the screen
1. Press "Enter"
1. Use the arrow keys to highlight the "Primary" option at the bottom of the screen
1. Press "Enter", then "Enter" a second time to confirm the default partition size
1. Use the arrow keys to highlight the "Bootable" option at the bottom of the screen
1. Press "Enter" -- this will set the "Boot" flag in the grey highlighted row in the table up top
  
    ???+ note "Not Sure if the Boot Flag Was Set?"        
         _This is a little weird if you haven't created partitions before since the screen behaves differently from the preceding ones.
         If you examine the table on this screen, you'll see a header row and a data row.  The headers will read things like "Name", "Flags", "Part Type", "FS Type", etc.  Look at the "Flags" column.  If the word "boot" appears beneath it then you're good!  If not, use the arrow keys to highlight the "Bootable" option at the bottom of the screen again and press "Enter"._
  
1. Use the arrow keys to highlight the "Write" option at the bottom of the screen
1. Press "Enter"
1. The program will ask you to confirm your choice.  This time, type out the word "yes" and press "Enter"
1. Once it finishes, use the arrow keys to highlight the "Quit" option at the bottom of the screen
1. Press "Enter"
1. On the next screen, make sure your new disk is highlighted (it'll read something like `sda1 unknown 8.00GB VBOXHARDDISK`)
1. Press "Enter"
1. Use the arrow keys to highlight the "ext4" option
1. Press "Enter"
1. Use the arrow keys to highlight the "Yes" option
1. Press "Enter"
1. Use the arrow keys to highlight the "Yes" option again
1. Press "Enter" to install the GRUB bootloader
1. Use the arrow keys to highlight the "Yes" option again
1. Press "Enter" to mark /system as read/write
1. Once the installation is finished, highlight "Run Android-x86"
1. Press "Enter"
  
You'll get dumped to a `console:/` line briefly.  After a couple seconds you should see the Android boot screen. Nice!  
  
### Ejecting the Installation Media

Once Android boots you'll see their installation wizard.  Before we continue, let's eject the installation media:

1. Press Cmd + Q (mac) or Ctrl + Q (windows) to open the Virtual Machine's power management window
1. Select "Power off the machine"
1. Click "OK"
1. Select your Virtual Machine from the VirtualBox sidebar
1. Click "Settings"
1. Click "Storage"
1. Highlight the Android ISO file in the devices list (it'll have a CD icon next to it)
1. Click the CD icon next to the "Optical Drive" dropdown in the "Attributes" section on the right
1. Choose "Remove Disk from Virtual Drive"
1. Click "OK"

### Booting Android for the First Time

The next steps are pretty straightforward:

1. Make sure the Virtual Machine is still highlighted and click "Start"
1. This time you'll see a console screen with a list of options.  Wait ~5 seconds and Android will start up
  
You'll now see the installation wizard again:

1. Click "Start"
1. On the "Connect to Wi-Fi" screen, click "See All Wi-Fi Networks"
1. Select "VirtWifi" from the list (VirtualBox will passthrough your internet connection to Android; you don't actually have to be connected to WiFi if your internet is supplied by an ethernet cable)
1. Android will check for updates; if any are available go ahead and install them.  Otherwise this step may take a while so just be patient
1. Choose "Don't Copy" on the "Copy apps & data" screen
1. On the Sign-in screen, enter your Google account credentials
    - In my case, I used my work account since the purpose of this VM is to collect the junky apps required by my employer
  
    ???+ note "Skipping Google Sign-In"
         _You may optionally choose to skip the Google Sign-In step.  This may prevent you from installing Play store apps, though._  
  
1. Set your date and time as needed
1. Optionally, turn on/off Google Services to your liking, scroll down and click "Accept"
1. Optionally, add a screen lock pattern/PIN/Password to your VM (in this case, a Password is probably the best option)
1. Optionally, skip or configure Google Wallet
1. Select "No Thanks" on the "Anything Else?" screen
1. Click "Quickstep" on the Home Screen picker
1. Click "Always"
  
Congratulations! You now have Android running inside a Virtual Machine.  
  
## Updating the System

By default some of the included apps and services will likely be outdated.  We'll want to update them first
before doing anything else:

1. Open the "Play Store"
1. Click the Hamburger menu in the top-left of the Play Store window
1. Click "My Apps & Games"
1. Highlight the "Updates" tab if it's not selected by default
1. Click the "Update All" button, if it's available
  
I found the initial downloads can get stuck.  If this happens, try stopping the downloads and updating the apps individually.  You may have to work at this a little bit and a restart or two also can't hurt -- hey, I never said it'd be perfect!  
  
You may also need to update Google Play Services separately.  To do that, click/drag from the top status bar downwards using your mouse to reveal the notification shade.  Look for a Google Play Services notification -- click that to open its updater page and apply it.  
  
## Taking a Snapshot

One of the great benefits of Virtual Machines is the "Snapshot" feature.  This lets you create a permanent
capture of the state of the system.  Further, you can take multiple of these and jump back-and-forth between them.
Installed an app or made a change that broke something? Just restore a snapshot!  We're going to take an intial
snapshot so we can return to a very clean system at any time (and avoid having to do all the complicated steps
above again if we break something).

1. Press Cmd + Q (mac) or Ctrl + Q (windows) to open the power management window
1. Choose "Power off the machine"
1. Click the "Take" button in VirtualBox's top menu bar (it's the camera icon with a big "+" sign overlaid on it)
1. Give the snapshot a name (I used "Base_Install") and add a description if you'd like
1. Click "OK"
  
Boom! Now you can return your Virtual Machine to this state at any time by highlighting it and clicking the "Restore" button.  
  
## Installing an App

Now you can go crazy.  Start up the Virtual Machine again and launch the Play Store.  You can click into the search
bar and use your keyboard and enter key to type out a search.  For example, try finding "Adobe Acrobat".  You can
click on any of the search results to open the app's landing page.  From there, click the Install button and in a
few moments you'll see the app icon appear on your home screen.  Clicking on the app icon will open it up, nice!  
  
As a caveat, not all apps may work inside the emulator (I had trouble getting Amazon Care to run).  In these
cases some more involved tinkering may be required, or, in worst cases you may simply be out of luck.  Otherwise
most things run well (except maybe mobile games!).  
  
Hopefully this is useful.  And if you are an IT person, please, don't forget that not everyone is fortunate enough
to benefit from technology you may take for granted.  

[android-x86]:https://www.android-x86.org/
[vbox]:https://www.virtualbox.org/
[stacks]:https://www.bluestacks.com/
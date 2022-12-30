# How To Install HP Printer/Scanner Drivers & Software  
The HP team provides linux-native drivers for many of their printers and scanners.  Unfortunately it seems like their installation packages are often out of date and don't fully support all spins of Debian/Ubuntu, like Pop! OS.  Generally speaking, HP Printers work fine out of the box, but if you want full wifi printing & scanning support you will likely need their "HPLIP" driver & software package.  
  
The notes below describe how I was able to install the full software suite on Pop! OS 22.04.  
  
## Getting Started  
As usual, the HPLIP software included in the Ubuntu/Pop! OS repositories is often out-of-date.  We'll need to do two things:  
  
1. Install a more up-to-date copy of the HPLIP software
2. (Optional) Install a proprietary binary which will allow you to take full advantage of your printer's features (as well as its scanning features, if you have a print/scan combo printer)  
  
## Installing HPLIP  
  
1. Add the following PPA:
    ```
    sudo add-apt-repository ppa:kelebek333/hplip -y
    ```
1. Update your apt cache:
    ```
    sudo apt update
    ```
1. Install the HPLIP command-line utility, and the HPLIP GUI utility:
    ```
    sudo apt install hplip hplip-gui -y
    ```
  
## Configuring HPLIP for Printing
  
1. Open HPLIP Toolbox
1. Connect your printer to your computer over USB OR if it is WiFi enabled, find its IP address
1. Click the green "+" to "Setup a new Device"
1. Select Universal Serial Bus (USB) if you are connecting over USB, OR in my case I selected "Network/Ethernet/Wireless network (direct connection or JetDirect)"
    1. Select "Show Advanced Options"
    1. Click the box next to "Manual Discovery"
    1. Key in the IP address of the printer and leave the JetDirect port at "1"
1. Click "Next"
1. If all goes well, the HP Device Manager window will show your printer's Model, IP Adress, Host Name, and Device URI
1. Click "Next" again

At this point you should be able to use the printing features of your unit.  But we're not done yet -- let's also set up scanning.

## Installing the HP Proprietary Binary

For some reason the scanning features of my printer were not part of the open source HP drivers.  Also for some reason their plugin installation wizard seems to point to a dead resource, which caused the installation to fail whenever I tried to use it.  Great.  Let's go through the manual method so we can work around these headaches:  
  
1. Run the HPLIP Toolbox program
1. Go to "Help > About..."
1. Make note of your HPLIP software version - we'll need to download the matching proprietary binary
    1. At time of writing, mine was 3.22.2, which was a few versions behind the latest available on HP's site
1. Head over to https://www.openprinting.org/download/printdriver/auxfiles/HP/plugins/
1. You will need to download two files:
    1. hplip-YOUR_HPLIP_VERSION-plugin.run
    1. hplip-YOUR_HPLIP_VERSION-plugin.run.asc
1. Then use the terminal to run the installation script
    1. *Note: The installer advises against running the script with `sudo` but I couldn't get it to work without doing so -- the installer wouldn't accept my root password (because there was none)*
    ```
    $ cd "$HOME/Downloads"
    $ sudo sh hplip-YOUR_HPLIP_VERSION-plugin.run
    ```
1. Follow any prompts shown by the script and at the end of it you should be able to use all the features of your printer/scanner.  

*In my case I had to use the HPLIP Toolbox software to "Add" the printer twice to unlock the printing AND scanning features.  Not sure what that was about but it doesn't seem to be a problem.  Aside from the toolbox software itself, none of the other programs on my computer noticed two copies of the same printer.*
  
## Printing & Scanning Software
  
Here are a few applications you can install if your distro doesn't come with printing & scanning software by default:

### Printing
1. [GNU Image Manipulation Program](https://www.gimp.org/)
    1. *Install as snap/flatpak package* 
1. [GNOME Document Viewer](https://wiki.archlinux.org/title/GNOME/Document_viewer)

### Scanning
1. [GNOME Document Scanner](https://apps.gnome.org/app/simple-scan/)
1. [XSane](https://gitlab.com/sane-project/frontend/xsane)

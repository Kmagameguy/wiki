# Headless Raspberry Pi from Scratch

Settings up a raspberry pi for headless networking is pretty
simple, and a great way to experiment with ssh, networking, gnu/linux, and the commandline -- especially for beginners.  This
guide will walk you through first-time system setup.
  
## Pre-requisites:
- A wifi-enabled (or ethernet-port-having) Raspberry Pi board
- A power supply for the Raspberry Pi board
- A microSD card
- microSD card port or dongle for your computer
- An internet connection
  
## Flashing the OS
We'll be using raspbian, the official OS maintained by the
Raspberry Pi manufacturer.  It is a Debian-linux derivative which
means stability and familiarity for those who have used Debian or
its other popular derivatives (Ubuntu, Pop!, Mint, etc).  
  
### Basic Settings  
1. Download and install the [Raspberry Pi Imager][pi-imager] tool
1. Plug the microSD card into your computer
1. Open the Pi Imager
1. Click "Choose OS" and open Pi OS (other) > Raspberry Pi OS Lite (32-bit)
1. Click "Choose Storage" and select your microSD card
1. Press Ctrl + Shift + X to open the Pi imager's secret settings menu
  
### Hostname
1. Enable "Set Hostname:" and change it to something you like
    
    ???+ warning
         _Hostnames can ONLY use characters a-z, 0-9, and dash "-".
         Any other characters are invalid and may cause problems so
         avoid them!_
  
### SSH
1. Enable "Enable SSH" and set the radio button to "Use password Authentication"
1. Set a strong password and keep it somewhere safe -- you'll use this
every time you try to connect to your pi
    - _We'll change this to proper ssh key authorization later._
  
### OPTIONAL: Configure WiFi
  
If you'll be connecting to your network WiFi and not via ethernet cable:  
  
1. Enable "Configure wifi"
1. Type in your network's name into the "SSID" field
1. Type in your WiFi password into the "Password" field
1. Select the appropriate entry for your part of the world in the "Wifi country" field
  
### Locale
  
1. Enable "Set locale settings"
1. Select the appropriate time zone for your part of the world
1. Type in the appropriate keyboard layout for your part of the world
  
### Persistent settings
  
1. Disable "Enable telemetry".  Yuck.  
  
When all's done click "Save", then "WRITE".  After a short time the
pi OS will be written to the SD card and ready to ship!
  
## First Boot
Safely remove your SD card from your computer and plug it into the
Raspberry Pi.  As soon as you connect the Pi to a power source it will boot up!
Give it ~3-5 minutes to finish the boot process and then head over to your router's dashboard in your browser.
Everyone's router dashboard will look different, but there should be a way to see the name & IP address of
any connected devices.  Look for an entry that matches the hostname you set above.  In some cases
the pi's hostname may be reported as "none".  Just make note of any "suspicious" devices in the list and
their IP addresses.

1. With IP address(es) in hand, open your terminal
1. SSH to the new system with: `ssh pi@<your_hostname>` (e.g. `ssh pi@raspberrypi`)
    - _If you get an error, try using the IP address instead of the hostname, e.g.: `ssh pi@192.168.0.40`_
1. Enter the password you set in the Pi Imager's advanced tools section when prompted, then hit return/enter.  
  
You will now be connected to the Pi system's commandline over ssh.  This allows you to control the system remotely without
a keyboard, mouse, or monitored connected up to the system. Slick!  
  
## Use the Full SD Card

By default Raspbian may not "see" the full capacity of the SD card.  This can
be fixed quickly by issuing the following commands over the SSH connection:  
  
1. `raspi-config --expand-rootfs`
1. `sudo reboot`

The second command will restart the system, naturally disconnecting your SSH connection.
  
## OPTIONAL - RASPBERRY PI 4 ONLY: Use 64bit Kernel

The newer Raspberry Pi 4 boards can run the 64-bit linux kernel. This can provide
some performance improvements over the 32-bit kernel so it's usually worth enabling:  
  
1. Over an SSH connection, run: `sudo nano /boot/config.txt`
1. Add the following line to the very end of the file (on a new line):
    `arm_64bit=1`
1. Press Ctrl + X to enter "save" mode, then "Y" to confirm the edits, then return/enter to commit the changes to disk
1. Run: `sudo reboot`  
  
You can then verify whether the system is using the 64bit kernel:  
  
1. Reconnect over SSH
1. Run `uname -a`
1. The output should include `aarch64`. If you see this then you are on the 64bit kernel 

## Assign a Static IP Address
  
The last thing we need is for the Pi's IP address to change on us periodically.  So, we'll set a static IP address to make sure we can always find it:  
  
1. Make sure you have an active SSH connection to the Pi (see steps above)
1. Type `sudo nano /etc/dhcpcd.conf` to open an in-terminal text editor (you'll be prompted for the pi user's password).  Add the following snippet to the very bottom of the file:  

```
interface wlan0 (or use eth0 if your Pi is connected to the router via ethernet cable)
static ip_address=<DESIRED_IP>/24
static routers=<ROUTER_IP>
static domain_name_servers=<ROUTER_IP>
```
  
Explanation:  
  
- routers: The IP address of your ROUTER (usually something like 192.168.0.1)
- ip_address: The desired network address you want to reach your Pi at.  This should be the same as your Router's IP but with the final number modified, e.g.: 192.168.0.30
    - _Don't exceed a value of 255_
    - _Pick a number that isn't already in use by one of your connected devices_
- domain_name_servers: The IHP address of your ROUTER (again)  
  
Continuing with the edits:  
  
1. Press Ctrl + X to set nano into "save" mode
1. Press "Y" then return/enter to write the changes to disk
1. Lastly enter the command `sudo reboot` and press return/enter  
  
The Pi system will restart so it can pick up the configuration changes.  Your ssh session will automatically disconnect too.  
  
## Configuring User Credentials

Next we'll rename the default user (pi).  The default username is easy for an
attacker to guess, since, well, it's the default for all fresh Raspbian
installations.  Changing it to something else will make it more memorable for us,
and less guessable for any potential threats.  We can't change the pi username
while we're logged into the account, so we will temporarily make use of
the ROOT account:

1. SSH to the Pi
1. Run `sudo passwd` to set a root user password (remember it for now but it'll be irrelevant later)
1. Run `sudo nano /etc/ssh/sshd_config` to open the ssh config file in nano for editing
1. Change this line:  
  
    `#PermitRootLogin prohibit-password`  
  
    TO  
  
    `PermitRootLogin yes`  
  
    (note the missing octothorpe)  
  
1. Press Ctrl + X, Y, return/enter to save the changes
1. Run `sudo reboot`  
  
Now, we'll connect as the ROOT user instead of the Pi user once the system starts
up again:  
  
- Run `ssh root@<pi_hostname_or_ip_address>` and enter the ROOT password you just set  
  
Change the "pi" username to something new.  This can be any name you want so long
as your remember it:  
  
1. Run `usermod -l <new_user> pi usermod -m -d /home/<new_user> <new_user>`
    - Example: `usermod -l larry pi usermod -m -d /home/larry larry`
1. Drop the SSH connection with Ctrl + D
1. Reconnect the SSH session using the new username (same password as the old pi user): `<new_user>@<pi_hostname_or_ip_address>`
1. Run `sudo passwd -l root` to disable the root user entirely (for security)
1. Run `sudo nano /etc/ssh/sshd_config`
1. Undo the changes you made earlier:  
  
    `PermitRootLogin yes`  
  
    BACK TO  
  
    `#PermitRootLogin prohibit-password`  
  
    (note the return of octothorpe)  
  
1. Drop the SSH connection with Ctrl + D  
  
Now you have a new username and the ROOT account is protected.  
  
## Configuring SSH

Next we'll work on strengthening our ssh connection.  This is especially
important if the Pi will be exposed to the public internet.  Otherwise,
in addition to being more secure, ssh key pairs are also more convenient
to use since they (can) safely work passwordless.  
  
On the host machine create a new key/pair with the ssh-keygen tool.  If you're on
linux already this should be installed by default.  If not, you may need to look up
a guide for your operating system:  
  
1. Create a new pair with `ssh-keygen -t ed25519` and follow the terminal prompts
    - _Give the key a useful name, like myserver\_rsa_
    - _You CAN add a password if you'd like. This is a good idea if the server will be accessible from the public internet. If it will only be accessible to your local network you can forego the password option_
    - _This command will create two files: myserver\_rsa and myserver\_rsa.pub._
    _Never ever ever share the myserver\_rsa file.  This is your private key._
    _Think of it like a password -- you don't want anyone to know it.  The .pub file on the other hand you can share around wherever you like!_
1. Install the new PUBLIC key on the server: `ssh-copy-id -i /path/to/myserver_rsa.pub <pi_username>@<pi_hostname_or_ip_address>`
1. SSH to the Pi -- it should happen without a password prompt (unless you added a password to the SSH key itself)  
  
With our key/pair set up we can disable ssh connections via plain-old password
authentication for even better security:  
  
1. Over an SSH connection, run `sudo nano /etc/ssh/sshd_config` to edit the Pi's ssh config again
1. Change the following:  
  
    `#PasswordAuthentication yes`  
  
    TO  
  
    `PasswordAuthentication no`  
  
    (note the missing octothorpe)  
  
1. Press Ctrl + X, Y, return/enter to save the changes
1. Run: `sudo reboot`  
  
And that is pretty much it!  From here you may want to explore other security
software, such as a firewall (UFW) and an intrusion detector/logger (Fail2Ban),
especially if this Pi will be serving applications that are accessible from the public internet.  Other good choices include wireguard, which will set up a VPN
that you can use to access your network on the go, or NGINX which could be used
as a simple webserver or reverse proxy.  
  
[pi-imager]:https://www.raspberrypi.com/software/

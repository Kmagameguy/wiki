# Making WiFi Work

At time of writing I'm using a 5GHz-band enabled USB dongle.  The first thing to do is to look up the model online and figure out what chipset the stick uses.  In my case it's an RTL8812au (realtek).

From there I was able to find a driver that worked for me.  You may have to do extensive research because there may be multiple options available, and each may only work with certain distros and/or kernel versions.  It's kind of a mess out there.


## Pre-Requisites
-  DKMS for automatically recompiling drivers after kernel updates

```
$ sudo apt install dkms
```
- Build essential tools to compile the drivers

```
$ sudo apt install build-essential libelf-dev linux-headers-`uname -r`
```

## Compiling & Installing the Driver

1. Clone the repo somewhere useful, such as `/home/$USER/drivers`
1. Follow the README.md as different packages will probably require slightly different installation or compilation steps; in my case:

```
$ sudo make dkms_install
```

Alternatively if you'd like to manually compile & install the driver:

```
sudo make && make install
```

## Configuring WiFi Parameters

Usually this is enough to get a baseline WiFi connection going.  However in most cases the connection speed is abysmal.  Some further tweaks that can help get speeds back up to par are below. AFAICT just running through all three causes no harm (some may need to be re-applied after major updates):

### Turn off WiFi Power Management

- Open the power management conf for editing:

```
$ sudo nano /etc/NetworkManager/conf.d/default-wifi-powersave-on.conf
```

- Change the value from 3 to 2 & save.


### Remove backport-iwlwifi-dkms Package

See: <https://bugs.launchpad.net/ubuntu/+source/backport-iwlwifi-dkms/+bug/1869588>

```
$ sudo apt remove backport-iwlwifi-dkms
```

### Instruct Pop! OS to Prefer IPv4 over IPv6

See: <https://askubuntu.com/questions/764387/very-slow-internet-connection-on-ubuntu-16-04>

Open the following conf file for editing:

```
$ sudo nano /etc/gai.conf
```

Then find the `Precedence ::ffff:0:0/96 10`  line and remove the leading `#` to uncomment the setting.  This will turn off power-saving mode on the WiFi dongle.

---

Reboot after applying these changes and run a speedtest.

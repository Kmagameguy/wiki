# Configuring a Logitech G600 Mouse
By default the Logitech team does not provide GHub or Logitech Gaming Software for Linux.  This means we have to get creative if we want to map our MMO mouse's buttons to keypresses and macros.  Enter [libratbag][libratbag] and [Piper][piper].

## Libratbag & Libratbagd

libratbag is a daemon that provides an API for Piper to use when translating button assignments to input device signals.  The project supports many devices out of the box, including the G600. Fantastic.

To install:

- Add the PPA:

```
$ sudo add-apt-repository ppa:libratbag-piper/piper-libratbag-git
```
- Update APT:

```
$ sudo apt update
```

- Install the daemon:

```
$ sudo apt install libratbagd
```

- Start the service:

```
$ sudo systemctl daemon-reload
$ sudo systemctl reload dbus.service
```

- Start the system automatically using systemctl:

```
$ sudo systemctl enable ratbagd.service
```

Next we'll install Piper which provides a GUI for configuring the mouse.

## Piper
To install:

- The PPA from the libratbag section also includes the Piper application.  Nice.  Install it with:

```
$ sudo apt install piper
```

- Run Piper and start plugging away at the settings.  Some tips & tricks:
    - Use "Send Keystroke" to create multi-button "macros"
    - Use Pop! OS' Keyboard Settings panel to create shortcuts to useful system actions that you can then use in PIper (e.g. volume up / down / mute)



[libratbag]: https://github.com/libratbag/libratbag
[piper]: https://github.com/libratbag/piper

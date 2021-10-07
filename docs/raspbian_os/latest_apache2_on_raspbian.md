# Install Latest Apache2 On Raspbian

As with most distributions, raspbian does not always come packaged
with the latest copy of the apache2 webserver.  If you use apache2 to host
a service (such as nextcloud) you may want to replace the
built-in repo with the latest upstream build from Debian.  This ensures critical security
fixes are available more quickly.

## How to Configure Raspbian

Start by adding the upstream repository and its gpg keys to apt's source list.
Make sure `wget` is available, then:

```
sudo wget -O /etc/apt/trusted.gpg.d/apache2.gpg https://packages.sury.org/apache2/apt.gpg
echo "deb https://packages.sury.org/apache2/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/apache2.list
sudo apt update
```

Then, install the latest apache:

```
sudo apt install -y apache2
```

To confirm whether the update worked, run:

```
apache2 -v
```

The commandline should print the now-installed apache2 version, which
you can compare to the PPA's webpage.  The version on the system
should match the latest version described in the PPA details:  
<https://launchpad.net/~ondrej/+archive/ubuntu/apache2>

You can also restart the server manually if you'd like to make sure the
updates are in use:

```
sudo service apache2 restart
```

# MQTT

MQTT (Messaging Queueing Telemetry Transport) is a publish-subscribe
protocol that helps bridge communications between disparate applications.
In short, you can use it to send a message from one application to another.

## Installing an MQTT Broker

A broker is required to serve as the "hub" for server/client connections.
We can use `mosquitto` for this:

```
$ sudo apt install mosquitto mosquitto-clients
```

## Configuring MQTT Broker

### Stop the Service First

```
$ sudo systemctl stop mosquitto.service
OR
$ sudo /etc/init.d/mosquitto stop
```

### Configure the Service

Edit the configuration file (default location below):

```
$ sudo nano /etc/mosquitto/mosquitto.conf
```

Example configuration:

```
# Place your local configuration in /etc/mosquitto/conf.d/
#
# A full description of the configuration file is at
# /usr/share/doc/mosquitto/examples/mosquitto.conf.example

pid_file /var/run/mosquitto.pid

persistence true
persistence_location /var/lib/mosquitto/

log_dest file /var/log/mosquitto/mosquitto.log
log_type error
log_type warning
log_type notice
log_type information

connection_messages true

log_timestamp true

include_dir /etc/mosquitto/conf.d
```

### Restart the MQTT Service

```
$ sudo systemctl start mosquitto.service
OR
$ sudo /etc/init.d/mosquitto start
```

## Testing the MQTT Broker

You can test the publish/subscribe handshake before setting up any
channels to confirm your configuration is good.  Do that like this:

1. Open two terminal windows (or two ssh sessions)
1. In one terminal, do: `$ mosquitto_sub -d -t hello/world`
    - *_sub* = subscribe (or listen) for messages
    - *-d* = debug the output
    - *-t* = which thread (channel) to listen on
    - *hello/world* = the name of the thread (channel) to communicate over
1. In the second terminal, do: `$ mosquitto_pub -d -t hello/world -m "Test Message"`
    - *-m* = tell mosquitto to send a message
    - *"Test Message"* = the content of the message we're sending
1. If all is well you should see the "Test Message" text appear in the first
terminal window

## Setting a Password

Defining a username & password for the client(s) that want to connect to the
service is a no-brainer.  Do that like so:

```
$ sudo mosquitto_passwd -c /etc/mosquitto/passwd <username>
```

This will let you specify a username.  Set it to anything you want (e.g. mosquittousr)
then run the command. The terminal will automatically ask ou to create a password
(store this somewhere safe).  
  
You can disable anonymouse connections to the service by doing the following:

1. Edit the mosquitto config file at: `$ sudo nano /etc/mosquitto/conf.d/default.conf`
1. Add these lines:
    ```
    allow_anonymous false
    password_file /etc/mosquitto/passwd
    ```
1. Restart the mosquitto service: `$ sudo systemctl restart mosquitto.service`

# Running Grafana Server on Ubuntu Focal Fossa (20.04 LTS)
  
While consolidating some of my homelab services onto a new server I had a bear of a time getting grafana-server to run on Ubuntu 20.04.  The first issue was that I couldn't seem to get the service to work on a different port from the default.  The second issue was that the service refused to start due to some systemd tomfoolery.  I'm writing these notes in hopes that they:  
  
1. Help someone else
1. Remind me what to try the next time I need to set up Grafana  
  
## Installation  
  
Start by installing grafana through apt:  
  
```
sudo apt install grafana
```
  
At this point my installation was broken -- systemd couldn't start the service.  
  
## Change the Port  
  
To change the port I had to edit three files:  

1. File 1: `/etc/grafana/grafana.ini`
1. File 2: `/usr/share/grafana/conf/defaults.ini`
1. File 3: `/usr/share/grafana/conf/sample.ini`
  
In each one:  

1. Find the line that reads `;http_port = 3000`
1. Change it to `http_port = [NEW_PORT_NUMBER]`
    - _Note the removal of the semi-colon, which uncomments the line_
  
## Edit the Service File  
  
For whatever reason the default service file doesn't seem to play nice with systemd on 20.04.  I had to comment out a ton of lines.  Here's the service file in full so it's easy to copy & paste:  
  
```
[Unit]
Description=Grafana instance
Documentation=http://docs.grafana.org
Wants=network-online.target
After=network-online.target
After=postgresql.service mariadb.service mysql.service

[Service]
EnvironmentFile=/etc/default/grafana-server
User=grafana
Group=grafana
Type=simple
Restart=on-failure
WorkingDirectory=/usr/share/grafana
RuntimeDirectory=grafana
RuntimeDirectoryMode=0750
ExecStart=/usr/sbin/grafana-server                                                  \
                            --config=${CONF_FILE}                                   \
                            --pidfile=${PID_FILE_DIR}/grafana-server.pid            \
                            --packaging=deb                                         \
                            cfg:default.paths.logs=${LOG_DIR}                       \
                            cfg:default.paths.data=${DATA_DIR}                      \
                            cfg:default.paths.plugins=${PLUGINS_DIR}                \
                            cfg:default.paths.provisioning=${PROVISIONING_CFG_DIR}  


LimitNOFILE=10000
TimeoutStopSec=20
#CapabilityBoundingSet=
#DeviceAllow=
#LockPersonality=true
#MemoryDenyWriteExecute=false
#NoNewPrivileges=true
#PrivateDevices=true
#PrivateTmp=true
#ProtectClock=true
#ProtectControlGroups=true
#ProtectHome=true
#ProtectHostname=true
#ProtectKernelLogs=true
#ProtectKernelModules=true
#ProtectKernelTunables=true
#ProtectProc=invisible
#ProtectSystem=full
#RemoveIPC=true
#RestrictAddressFamilies=AF_INET AF_INET6 AF_UNIX
#RestrictNamespaces=true
#RestrictRealtime=true
#RestrictSUIDSGID=true
#SystemCallArchitectures=native
UMask=0027

[Install]
WantedBy=multi-user.target
```
  
## Restart Grafana Server  
  
With everything reset you should be able to start up the Grafana service.  I had to run these commands to clear everything up:  
  
```
$ sudo systemctl stop grafana-server.service
$ sudo systemctl reset-failed grafana-server.service
$ sudo systemctl start grafana-server.service
```
  
You can check whether the server is working by opening the server URL:PORT in a browser, or by checking it through systemd: `systemctl status grafana-server.service`.  
  
If everything looks good the last thing to do is make Grafana run at startup:  
  
```
sudo systemctl enable grafana-server.service
```
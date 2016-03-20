# Android-Table-as-Raspberry-Pi-display-tethered

# Raspberry Pi
## Configure networking via the USB cable

### Install [tightvncserver](http://www.tightvnc.com/) or equivalent and install as follows;

```bash
sudo apt-get install tightvncserver
```

### Edit the interfaces file adding a USB connect (for teathering)
```bash
sudo nano /etc/network/interfaces
```

#### Using the gateway address obtained as follows
```bash
arp -a
```

#### Append the following into the interfaces file
```bash
auto usb0
allow-hotplug usb0
iface usb0 inet static
address 192.168.42.144
netmask 255.255.255.0
network 192.168.42.0
#gateway 192.168.1.254
broadcast 192.168.42.255
```

### Restart the network
```bash
sudo  /etc/init.d/networking restart
```

### Ensure tightvnc starts automatically on power on

#### Create a new service to start tightvncserver (using systemd is earlier systemv then exercise up to reader)
```bash
sudo nano /etc/systemd/system/tightvncserver.service
```

#### add the following;
```bash
[Unit]
Description=TightVNC remote desktop server
After=sshd.service
 
[Service]
Type=dbus
ExecStart=/usr/bin/tightvncserver :1 -geometry 1280x800
User=pi
Type=forking
 
[Install]
WantedBy=multi-user.target
```

#### Set ownership, executable and enable
```bash
sudo chown root:root /etc/systemd/system/tightvncserver.service
sudo chmod 755 /etc/systemd/system/tightvncserver.service
sudo systemctl enable tightvncserver.service
```

# Android Tablet
If your nexus 7 2012 is a wifi only version then you cannot access the tethering options from the stock install.
Unfortunately you need to root the device and install Cyanogenmod in order to link via usb cable.

# How to install Cyanogenmod on the nexus 7 2012
Install the latest SDK for Android

Follow the (instructions)[https://wiki.cyanogenmod.org/w/Install_CM_for_grouper] _very_ closely.
Even with these instructions it took a few hours to figure out the exact images I should be installing.
For the nexus 7 I used the following;

- CM core image - (cm-12.1-20151117-SNAPSHOT-YOG7DAO1KA-grouper.zip)[https://download.cyanogenmod.org/get/jenkins/135177/cm-12.1-20151117-SNAPSHOT-YOG7DAO1KA-grouper.zip] 
- Recovery image - (twrp-2.8.7.0-grouper.img)[https://dl.twrp.me/grouper/twrp-2.8.7.0-grouper.img]
- Google apps - (gapps-lp-20150222-signed.zip)[http://www.idadtech.com/download-gapps-on-cyanogenmod-12-cm12-lollipop-5-x]

These can all be found from the CM page above.

# Post cyanogenmod installation
Plug in the cable between the tablet and RPi
Open settings and enable tethering
_Back at the RPI cli run ifconfig and you should see usb0 appear with the settings_
Using the play store install either bVNC or VNC viewer
- Set the URL to the static IP for the USB0 of the PI
- Set the port to 5901
- Set the password

You will now connect to the RPi via the cable and can use your RPi without requiring a network.


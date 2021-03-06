# tr-prepare-host


# Rename Network Interface Names #

# Setup Network Infrastructure #

```
sudo systemctl enable systemd-networkd
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved
sudo rm /etc/resolv.conf
sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
```

```
sudo mkdir /etc/systemd/network
```


```
cat /etc/systemd/network/20-net.network
[Match]
Name=net*

[Network]
DHCP=yes
```

```
cat /etc/udev/rules.d/10-network.rules
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:79:2a:e8:53:23", NAME="term00"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:79:2a:e8:53:22", NAME="term01"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:79:2a:e8:53:20", NAME="net00"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="dc:85:de:80:e6:74", NAME="wifi00"
```

Remove now handled interface information from networkd, disable ifup/debian mechanism.
Just remove all information from `/etc/network/interfaces`, just leave:

```
auto lo
iface lo inet loopback
```

## Setup WiFi Access Point

Requirements:

- No Password for now
- Matches IP address Range

```
sudo apt-get install -y hostapd dnsmasq

sudo nano /etc/hostapd/hostapd.conf
interface=wlan0
driver=nl80211
ssid=NotFreeWifi
channel=1

sudo nano /etc/init.d/hostapd
DAEMON_CONF=/etc/hostapd/hostapd.conf

sudo nano /etc/dnsmasq.conf
log-facility=/var/log/dnsmasq.log
address=/#/10.0.0.1
interface=wlan0
dhcp-range=10.0.0.10,10.0.0.250,12h
no-resolv
log-queries
```
Network

```
# /etc/systemd/network/50-static.network
[Match]
Name=wifi*

[Network]
Address=192.168.0.15/24
Gateway=192.168.0.1
```


http://andrewmichaelsmith.com/2013/08/raspberry-pi-wi-fi-honeypot/

# Enable Forwading

```
sudo vim /etc/sysctl.d/99-sysctl.conf
```

# Install Software

```
sudo apt-get install python3-pip nftables
```

```
mkdir src
cd src
git clone https://github.com/hgn/foo-conf.git
git clone https://github.com/hgn/ohnld.git
git clone https://github.com/hgn/route-manager.git
git clone --recursive https://github.com/protocollabs/dmprd.git
```


# Misc Information #


First find PCI bus information to current naming scheme, so that you identify the slots:

```
sudo lshw -class network | egrep "logical name|bus info" -
```

Or

```
udevadm info /sys/class/net/[tab]
```

Add files for each network interfaces with the similar content:

```
[Match]
Path=pci-0000:00:1a.0-*

[Link]
Name=internet0
```

NOTE: this will not work for Ubuntu 16.04 - you can enable systemd-networkd - but they silently skip the .link components. Meaning udev is kicking in as normal but systemd will not touch the configuration. Bummer!

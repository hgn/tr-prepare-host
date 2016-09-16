# tr-prepare-host


# Rename Network Interface Names

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

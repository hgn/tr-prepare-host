# tr-prepare-host


# Rename Network Interface Names

First find PCI bus information to current naming scheme, so that you identify the slots:

```
sudo lshw -class network | egrep "logical name|bus info" -
```

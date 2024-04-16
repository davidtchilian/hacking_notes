
## Bettercap

The Swiss Army knife for [WiFi](https://www.bettercap.org/modules/wifi/), [Bluetooth Low Energy](https://www.bettercap.org/modules/ble/), wireless [HID hijacking](https://www.bettercap.org/modules/hid/) and [IPv4 and IPv6](https://www.bettercap.org/modules/ethernet) networks reconnaissance and MITM attacks.

Read the [project introduction](https://www.bettercap.org/intro/) to get an idea of what bettercap can do for you, [install](https://www.bettercap.org/installation/) it, [RTFM](https://www.bettercap.org/usage/) and start **hacking all the things!!!**

```shell
net.probe on # scans the devices on wifi network
net.show # shows the devices
# copy the ip address of device to scan
set arp.spoof.targets <ip>
arp.spoof on
net.sniff on
```


---
date: 2024-02-15
categories:
  - Tools
  - UNIX
---

# brltty and Generic Serial Converter

Distributions based on Ubuntu have
[brltty](https://brltty.app/){target="_blank"} enabled by default. Some braille
displays do not change the ids of those converters and therefore brltty prevent
the serial converter showing up as a serial device under `/dev/ttyUSBx`.

<!-- more -->

In early versions of brltty several generic serial converters (e.g., `cp210x`)
were affected due to [:material-ubuntu: bug #1958224](
https://bugs.launchpad.net/ubuntu/+source/brltty/+bug/1958224){target="_blank"}.
Since brltty version `6.4-4ubuntu3` this bug was fixed.

For other generic serial converter this problem still exists. The chipset
`ch341` that can be found in the [Ulanzi TC001](../../ha/devices/ulanzi_tc001.md)
desktop clock can not be used. The issue is described in [:material-ubuntu: bug #1990189](
https://bugs.launchpad.net/ubuntu/+source/brltty/+bug/1990189){target="_blank"}.

One solution is to disable the udev part of brltty with the following commands:

``` console
sudo systemctl stop brltty-udev.service
sudo systemctl mask brltty-udev.service
```

The main problem with this solution is that this disable all braille displays.

In the bug description above a better solution is described. This solution is
already implemented in the Debian packages of brltty. In Ubuntu this patch is
currently not available. The here described solution can be easily adopted to
other generic serial converters that are disabled by brltty.

Check for existing serial converter:

``` console
$ lsusb | grep "serial"
Bus 001 Device 037: ID 1a86:7523 QinHeng Electronics CH340 serial converter
```

In the log files the following can be found:

``` console
$ sudo journalctl -b
...
Feb 10 10:00:00 chumbucket kernel: ch341 1-3:1.0: device disconnected
Feb 10 10:00:00 chumbucket kernel: usb 1-3: new full-speed USB device number 37 using xhci_hcd
Feb 10 10:00:00 chumbucket kernel: usb 1-3: New USB device found, idVendor=1a86, idProduct=7523, bcdDevice=81.33
Feb 10 10:00:00 chumbucket kernel: usb 1-3: New USB device strings: Mfr=0, Product=2, SerialNumber=0
Feb 10 10:00:00 chumbucket kernel: usb 1-3: Product: USB Serial
Feb 10 10:00:00 chumbucket kernel: ch341 1-3:1.0: ch341-uart converter detected
Feb 10 10:00:00 chumbucket kernel: usb 1-3: ch341-uart converter now attached to ttyUSB0
...
```

If brltty is not interfere the `/dev/ttyUSB0` device must be existing:

``` console
$ ls -l /dev/ttyUSB0
crw-re---- 1 root dialout 188, 0 Feb 10 10:00 /dev/ttyUSB0
```

If brltty is interfering the device file is not existing and in the log files
the following lines show that brltty is catching the serial device:

``` console
$ sudo journalctl -b
...
Feb 10 12:07:44 chumbucket kernel: usb 1-3: usbfs: interface 0 claimed by ch341 while 'brltty' sets config #1
Feb 10 12:07:44 chumbucket kernel: ch341-uart ttyUSB0: ch341-uart converter now disconnected from ttyUSB0
Feb 10 12:07:44 chumbucket kernel: ch341 1-3:1.0: device disconnected
...
```

The problem can be found in the rule file `/usr/lib/udev/rules.d/85-brltty.rules`:

```
# Device: 1A86:7523
# Baum [NLS eReader Zoomax (20 cells)]
ENV{PRODUCT}=="1a86/7523/*", ENV{BRLTTY_BRAILLE_DRIVER}="bm", GOTO="brltty_usb_run"
```

Here every chipset with vendor id `1a86` and product id `7523` will be handled
as braille display. You can comment out these lines or adapt the patch of the
above bug description. In order to change the rules copy the file into
`/etc/udev/rules.d`:

``` console
cp /usr/lib/udev/rules.d/85-brltty.rules /etc/udev/rules.d/85-brltty.rules
```

And change the lines into:

```
# Device: 1A86:7523
# Generic Identifier
# Vendor: Jiangsu QinHeng, Ltd.
# Product: CH341 USB Bridge Controller
# Baum [NLS eReader Zoomax (20 cells)]
ENV{PRODUCT}=="1a86/7523/*", ATTRS{idVendor}=="1a40", ATTRS{idProduct}=="0101", ENV{BRLTTY_BRAILLE_DRIVER}="bm", GOTO="brltty_usb_run"
```

Now only a specific braille display will be used from brltty. All other `ch341`
serial converter will be ignored.

The changed rule set must be loaded via udev:

``` console
sudo udevadm control --reload && sudo udevadm trigger
```

Afterwards the brltty udev service must restarted:

``` console
sudo systemctl restart brltty-udev.service
```

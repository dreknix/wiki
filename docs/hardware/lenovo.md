# Lenovo ThinkPad

## Firmware Update

Download boot ISO image from Lenovo support.

Extract image from ISO:

```console
$ geteltorito -o e480.img ~/Downloads/r0puj37wd.iso
```

Get the device of the USB stick with `lsblk`. And write the image to the USB
stick.

```
$ sudo dd if=e480.img of=/dev/sdb bs=64K
```

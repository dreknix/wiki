# NVME CLI

## General usage

```console
$ sudo nvme list
```

```console
$ sudo nvme smart-log /dev/nvme0n1
```

Device performance with `htparm`:

```console
$ sudo hdparm -Tt --direct /dev/nvme0n1
```

## Update Firmware

Display available slots and installed firmware versions:

```console
$ sudo nvme fw-log /dev/nvme0n1
Firmware Log for device:nvme0n1
afi  : 0x1
frs1 : 5B2QGXA7
```

Check if firmware update is supported and which slots are available:

```console
$ sudo nvme id-ctrl -H /dev/nvme0n1 | grep Firmware
  [9:9] : 0x1   Firmware Activation Notices Supported
  [4:4] : 0x1   Firmware Activate Without Reset Supported
  [3:1] : 0x3   Number of Firmware Slots
  [0:0] : 0     Firmware Slot 1 Read/Write
```

Download firmware:

```console
$ sudo nvme fw-download -f firmware.enc /dev/nvme0n1
```

Commit firmware to slot 2 without activation (`-a 0`):

```console
$ sudo nvme fw-commit -s 2 -a 0 /dev/nvme0n1
```

Check with `nwme fw-log` if firmware is stored in slot 2. Than activate (`-a 2`)
the firmware in slot 2.

```console
$ sudo nvme fw-commit -s 2 -a 2 /dev/nvme0n1
```

### Samsung Firmware

https://semiconductor.samsung.com/consumer-storage/support/tools/

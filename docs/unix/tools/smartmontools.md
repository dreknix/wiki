# smartmontools

* [smartmontools](https://www.smartmontools.org/){target="_blank"}
* [smartctl(8)](https://manpages.debian.org/smartctl.8.en.html){target="_blank"}

## Get information about device

Scan for devices:

``` console
sudo smartctl --scan
```

In order to check if the device supports SMART and if SMART is enabled, use
option `--info` or `-i`:

``` console
sudo smartctl --info /dev/sda
```

If the device is not in the smartctl database, update the database with:

``` console
sudo /usr/sbin/update-smart-drivedb
```

## Enable SMART

``` console
sudo smartctl --smart=on --offlineauto=on --saveauto=on /dev/sda
```

## Get the capabilities of the device

Get the capabilities of the device with option `--capabilities` or `-c`:

``` console
sudo smartctl --capabilities /dev/sda
```

## Print the health status

Get the health status of the device with option `--health` or `-H`:

``` console
sudo smartctl --health /dev/sda
```

## Dump all information about the device

To get all SMART information about the device with option `--all` or `-a`:

``` console
sudo smartctl --all /dev/sda
```

Get all the information above and all non-SMART information about the device
with option `--xall` or `-x`:

``` console
sudo smartctl --xall /dev/sda
```

## Print error log

Print device error log with option `--log=error` or `-l error`:

``` console
sudo smartctl --log=error /dev/sda
```

## Performing self tests

Start self test with option `--test` or `-t`:

``` console
sudo smartctl --test=long /dev/sda
```

Get results of test:

``` console
sudo smartctl --log=selftest /dev/sda
```

## Access disks in RAID system

### MegaRAID

Get list of disk in RAID:

``` console
sudo /opt/MegaRAID/storcli/storcli64 /c0 /eall /sall show
```

Use the `DID` as disk number in the `smartctl` command:

``` console
sudo smartctl --info --device=megaraid,40 /dev/sda
```

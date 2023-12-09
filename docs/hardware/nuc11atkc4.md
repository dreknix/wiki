# NUC 11 Essential Kit

* [NUC 11 Essential Kit - NUC11ATKC4](
   https://www.intel.com/content/www/us/en/products/sku/217669/)
* Support
  * [Getting Started](
     https://www.intel.com/content/www/us/en/support/articles/000088662/intel-nuc.html)
  * [Support Site](
     https://www.intel.com/content/www/us/en/support/products/217669/intel-nuc/intel-nuc-kits/intel-nuc-kit-with-intel-pentium-processors/intel-nuc-11-essential-kit-nuc11atkc4.html)
  * [All Downloads](https://www.intel.com/content/www/us/en/download-center/home.html)

## Boot

* ++f2++ - Enter setup
* ++f7++ - Update BIOS
  * [ATJSLCPX](https://www.intel.com/content/www/us/en/download/721452/bios-update-atjslcpx.html) - Version 37
* ++f10++ - Boot menu
* ++f12++ - Network boot

## Debian 11 - bullseye

### Firmware

The following package must be installed:

* `firmware-linux-nonfree`
* `firmware-realtek`
* `firmware-iwlwifi`

```console
$ sudo apt install firmware-linux-nonfree firmware-realtek firmware-iwlwifi
```

### LUKS - Dropbear

```console
$ sudo apt install dropbear-initramfs
```

Create `/etc/dropbear-initramfs/authorized_keys` and update the initramfs.

```console
$ sudo update-initramfs -u
```

Unlock the LUKS encryption: `cryptroot-unlook`

### dmidecode

```console
$ sudo dmidecode -s system-product-name
NUC11ATKC4
$ sudo dmidecode -s system-serial-number
XXXXXXXXXXXX
$ sudo dmidecode -s bios-version
ATJSLCPX.0037.2022.0715.1547
```

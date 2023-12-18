# Framework 13 AMD

[Framework 13 AMD](
https://frame.work/de/en/products/laptop-diy-13-gen-amd){target="_blank"}

* AMD Ryzen 7 7840U
* AMD Radeon 780M Graphics
* DDR5-5600 - 32GB (2 x 16GB)
* WD Black SN850X 2TB
* MEDIATEK MT7922 802.11ax Wireless Network Adapter

## Debian 13 trixie (testing)

Basic [installation](../unix/debian/install.md) from network installer (netisnt)
ISO image with disk encryption and `btrfs` flat volume layout.

After the first reboot [tweak the fresh installation](../unix/debian/tweak.md).

### Install Missing Firmware

#### MEDIATEK MT7922

Install `mt7921e` for MEDIATEK MT7922 802.11ax Wireless Network Adapter:

``` console
sudo apt install firmware-misc-nonfree
```

#### AMD Radeon 780M

The package `firmware-amd-graphics` must not be installed:

``` console
sudo apt purge firmware-amd-graphics
```

Downloading specific firmware is described in the
[Debian Wiki](https://wiki.debian.org/Firmware){target="_blank"}.

``` console
mkdir firmware
cd firmware/
wget -r \
     -nd \
     -e robots=no \
     -A '*.bin' \
     --accept-regex '/plain/' \
     https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/amdgpu/
sudo mkdir /lib/firmware/amdgpu/
sudo mv *.bin /lib/firmware/amdgpu/
sudo update-initramfs -c -k all
``

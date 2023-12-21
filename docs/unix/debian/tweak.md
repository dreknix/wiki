# Tweak the Fresh Installation

After the first reboot tweak the fresh installation.

## btrfs

Fix permissions for `/.btrfs` and `/.snapshots`:

``` console
sudo chown root:sudo /.btrfs /.snapshots
sudo chmod 0750 /.btrfs /.snapshots
```

Install missing tools:

``` console
sudo apt install btrbk btrfs-compsize
```

## Use UUID in /etc/fstab

Get with `sudo blkid` the UUIDs of each partition and use the UUID instead of
the partition device name.

## LUKS

Unlock swap partition with key file instead of entering passphrase twice.

Create key file with 8196 bytes of random data:

``` console
sudo dd if=/dev/random bs=1024 count=8 of=/etc/cryptsetup-keys.d/nvme0n1p4_crypt.key
```

Add key file to LUKS disk encryption:

``` console
sudo cryptsetup luksAddKey /dev/nvme0n1p4 /etc/cryptsetup-keys.d/nvme0n1p4_crypt.key
```

Add key file to `/etc/crypttab`:

``` plaintext
nvme0n1p4_crypt UUID=415d02ee-fa95-41ba-9f18-f47df34dea0e /etc/cryptsetup-keys.d/nvme0n1p4_crypt.key luks,swap,discard
```

The key file must be present during boot time, so it must be included into the
initramfs image. Therefore, the path `/etc/cryptsetup-keys.d/` must be included
in the file `/etc/cryptsetup-initramfs/conf-hook`.

``` plaintext
KEYFILE_PATTERN="/etc/cryptsetup-keys.d/*.key"
```

Since now the key file is part of the initramfs image, the permissions need to
be set. Therefore, create a file `/etc/initramfs-tools/conf.d/umask` with the
following content:

``` plaintext
UMASK=0077
```

Create the initramfs image:

``` plaintext
sudo update-initramfs -u
```

Reboot the computer and test, if the swap partition is available.

## Enable SSH for unlocking LUKS

:construction: **TODO**

## Plymouth

Activate plymouth during boot for LUKS prompt.

Add `splash` to `GRUB_CMDLINE_LINUX_DEFAULT`:

``` plaintext title="/etc/default/grub"
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

Update GRUB configuration:

``` console
sudo update-grub2
```

## Configure zram

See the blog article about [zram](../../blog/posts/zram-and-swap.md).

## Hibernation

:construction: **TODO**

* disable secure boot
* `cat /sys/power/state`
* `/etc/initramfs-tools/conf.d/resume`
* `systemctl hibernate`

## Configure Power Settings

:construction: **TODO**

* lid close
* power button
* time outs

``` console
gsettings set org.gnome.settings-daemon.plugins.power ambient-enabled false
gsettings set org.gnome.settings-daemon.plugins.power idle-dim false
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-battery-type 'nothing'

gsettings set org.gnome.desktop.session idle-delay 600

gsettings set org.gnome.desktop.interface show-battery-percentage true
```

## Install additional Packages

``` console
sudo apt install lshw
```

:construction: **TODO** merge this with Ansible

## Finalize with Ansible

:construction: **TODO** describe the steps

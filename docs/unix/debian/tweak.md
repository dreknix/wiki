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

:construction: **TODO** unlock swap partition with file

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

:construction: **TODO**

## Install additional Packages

``` console
sudo apt install lshw
```

:construction: **TODO** merge this with Ansible

## Finalize with Ansible

:construction: **TODO** describe the steps

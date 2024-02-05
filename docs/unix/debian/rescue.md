# Rescue with Debian Live

## Disk Setup

Get an overview about the disk devices and their file systems:

``` console
sudo lsblk -f
```

### RAID

If some of the partitions have the type `linux_raid_member` a software RAID is
present.

Install the corresponding package if not present:

``` console
sudo apt install mdadm
```

If the RAID partitions are not created by the live system, try to recreate the
RAID partitions with:

``` console
sudo mdadm --assemble --scan
```

Information about available RAID disks can be found in `/proc/mdstat`. The RAID
based partition are named `/dev/mdXXX`.

If a disk is missing, try to recreate the RAID partitions by hand.

### LUKS

If some of the partitions have the type `crypto_LUKS` an encrypted partition is
present.

Install the corresponding package if not present:

``` console
sudo apt install cryptsetup
```

Unlock the LUKS device:

``` console
sudo cryptsetup luksOpen /dev/md126 crypt
```

The unlocked LUKS partition is now available with the device`/dev/mapper/crypt`.

### LVM

If some of the partitions have the type `LVM2_member` a software RAID is
present.

Install the corresponding package if not present:

``` console
sudo apt install lvm2
```

Available volume groups can be shown with:

``` console
sudo lvm vgs
```

In order to activate a volume group, enter the following command:

``` console
sudo lvm vgchange -ay XXXXX-vg
```

The available logical volumes can be shown with:

``` console
sudo lvm lvs
```

### Mount

Mount the directory structure:

``` console
mkdir /target
sudo mount /dev/XXXXX /target
sudo mount /dev/XXXXX /target/boot
sudo mount /dev/XXXXX /target/boot/efi
...
```

In order to use `grub-install` or other tools, the directories `/proc`, `/dev`,
and `/sys` must be mounted:

``` console
sudo mount -t proc -o nosuid,nodev,noexec proc /target/proc
sudo mount --rbind --make-rslave /dev /target/dev
sudo mount --rbind --make-rslave /sys /target/sys
```

!!! info

    Unmounting the file systems is simple. Only the `/dev` and `/sys` file
    system must be unmounted with the option `--recursive`.

    ``` console
    sudo umount --recursive /target/sys
    sudo umount --recursive /target/dev
    sudo umount /target/proc
    sudo umount /target/boot/efi
    sudo umount /target/boot
    sudo umount /target
    ```

## Rescue

Enter the system:

``` console
sudo chroot /target /bin/bash -il
```

### GRUB

**TODO**

* `update-grub`
* `grub-install`
* `efibootmgr --verbose`

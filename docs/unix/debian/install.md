# Installing Debian

## Debian 13 trixie (testing)

Get the
[network installer](
https://cdimage.debian.org/cdimage/daily-builds/daily/current/amd64/iso-cd/){target="_blank"}
from Debian as daily build.

* Copy ISO image to USB stick
  * `cp debian-testing-amd64-netinst.iso /dev/sdX`
* Boot the USB stick
  * `Advanced options ...` > `... Expert install`
* Configure Debian installer
  * `Choose language`
    * Language: `English`
    * Country: `other` > `Europe` > `Germany`
    * Default locale: `en_US.UTF-8`
    * Additional locales: none
  * `Configure the keyboard`: `German`
  * `Load installer components from installation media`
    * [x] `choose-mirror`
    * [x] `network-console`
  * `Detect network hardware`
  * `Configure the network`
  * `Continue installation remotely using SSH`
    * `Remote installation password`

Continue with installation via SSH:

* `ssh installer@IP-Addr`
  * `Start installer (export mode)`

Continue configuring Debian installer:

* `Choose a mirror of the Debian archive`
  * `http` > `Germany` > `ftp.de.debian.org`
* `Set up users and passwords`
  * Allow login as root: `No`
  * Full name of new user: `dreknix`
  * Username for your account: `dreknix`
  * Password:
* `Configure the clock`
  * Set the clock using NTP: `Yes`
  * NTP server to use: `0.debian.pool.ntp.org`
  * Time zone: `Europe/Berlin`
* `Detect disks`
* `Partition disks`
  * `Manual`
    * `Create a new partion`
      * Size: `2 GB`
      * Location: `Beginning`
      * Name: `EFI`
      * Use as: `EFI System Partition`
      * Bootable flag: `on`
    * `Create a new partion`
      * Size: `4 GB`
      * Location: `Beginning`
      * Name: `Boot`
      * Use as: `Ext4 journaling file system`
      * Mount point: `/boot`
    * `Create a new partion`
      * Size: `1.9 TB`
      * Location: `Beginning`
      * Name: `Crypto_Linux`
      * Use as: `physical volume for encryption`
      * Encryption key: `Passphrase`
      * Erase data: `no`
    * `Create a new partion`
      * Size: `94 GB`
      * Location: `Beginning`
      * Name: `Crypto_Swap`
      * Use as: `physical volume for encryption`
      * Encryption key: `Passphrase`
      * Erase data: `no`
    * `Configure encrypted volumes`
      * Write the changes to disk: `Yes`
      * `Create encrypted volumes`
        * Select both crypto partitions
      * `Finish`
        * Enter passphrase for both partitions
    * `Partition settings` - root crypto partition
      * Use as: `btrfs journaling file system`
      * Mount point: `/`
      * Label: `root`
    * `Partition settings` - swap crypto partition
      * Use as: `swap area`
    * `Finish partitioning and write changes to disk`
      * Write the changes to disk: `Yes`

Exit to shell in order to change the `btrfs` and `/tmp` configuration.

``` console
~ # df -h
Filesystem                Size      Used Available Use% Mounted on
tmpfs                     3.1G    532.0K      3.1G   0% /run
devtmpfs                 15.3G         0     15.3G   0% /dev
/dev/sda1               547.0M    547.0M         0 100% /cdrom
none                    147.9K     67.0K     75.9K  47% /sys/firmware/efi/efivars
/dev/mapper/nvme0n1p3_crypt
                          1.7T      5.8M      1.7T   0% /target
/dev/nvme0n1p2            3.6G     28.0K      3.4G   0% /target/boot
/dev/nvme0n1p1            1.9G      4.0K      1.9G   0% /target/boot/efi
```

Unmount the `/target` partitions:

``` console
~ # umount /target/boot/efi
~ # umount /target/boot
~ # umount /target
```

Mount the `btrfs` encrypted partition to `/mnt`:

``` console
~ # mount /dev/mapper/nvme0n1p3_crypt /mnt
~ # cd /mnt/
/mnt # ls
@rootfs
```

Change the name of the root subvolume:

``` console
/mnt # mv @rootfs @
```

Create other subvolumes:

``` console
/mnt # btrfs subvolume create @snapshots
Create subvolume './@snapshots'
/mnt # btrfs subvolume create @home
Create subvolume './@home'
```

Create the new mount structure under `/target`:

``` console
/mnt # cd /mnt
~ # mount -o compress=zstd:3,subvol=@ /dev/mapper/nvme0n1p3_crypt /target
~ # mount /dev/nvme0n1p2 /target/boot/
~ # mount /dev/nvme0n1p1 /target/boot/efi/
~ # mkdir /target/.snapshots
~ # mkdir /target/home
~ # mount -o compress=zstd:3,subvol=@snapshots /dev/mapper/nvme0n1p3_crypt /target/.snapshots
~ # mount -o compress=zstd:3,subvol=@home /dev/mapper/nvme0n1p3_crypt /target/home
```

!!! info

    The flags `ssd` and `space_cache=v2` are enabled by default.

    The flag `noatime` is not needed anymore. The default flag is `relatime`
    that prevents a lot of writes.

    The flag `discard=async` is also not needed anymore. **TODO**

Unmount the `btrfs` partition:

``` console
~ # unmount /mnt
```

Edit the `/target/etc/fstab` file:

```
/dev/mapper/nvme0n1p3_crypt  /            btrfs  compress=zstd:3,subvol=@           0   0
/dev/mapper/nvme0n1p3_crypt  /.snapshots  btrfs  compress=zstd:3,subvol=@snapshots  0   0
/dev/mapper/nvme0n1p3_crypt  /home        btrfs  compress=zstd:3,subvol=@home       0   0
```

!!! info

    Instead of device names the UUID of the partitions should be used. The get
    corresponding UUIDs use the command `blkid`.

!!! info

    Optional: the directory `/tmp` can be configured as `tmpfs`.
    **TODO** Why systemd tmp.mount should currently not be used in Debian

    Append the following line to `/target/etc/fstab`:

    ```
    tmpfs   /tmp   tmpfs   nosuid,nodev,strictatime,size=4GB,nr_inodes=1m,mode=1777   0   0
    ```

    Create the directory and mount it for installation:

    ```
    ~ # mkdir /target/tmp
    ~ # mount -t tmpfs -o nosuid,nodev,strictatime,size=4g,nr_inodes=1m,mode=1777 tmpfs /target/tmp
    ```

Exit the shell and continue with the installation process.

Continue configuring Debian installer:

* `Install the base system`
  * Kernel to install: `linux-image-amd64`
  * Drivers to include in the initrd: `generic: include all available drivers`
* `Configure the package manager`
  * Use a network mirror: `Yes`
    * `http` > `Germany` > `ftp.de.debian.org`
  * Use non-free firmware: `Yes`
  * Use non-free software: `Yes`
  * Enable source repositories in APT: `No`
  * Services to use:
    * [x] security updates (from security.debian.org)
    * [x] release updates
    * [ ] backported software
* `Select and install software`
  * Updates management on this system: `No automatic updates`
  * Participate in the package usage survey: `Yes`
  * Choose software to install:
    * [x] Debian desktop environment
    * [x] ... GNOME
    * [x] SSH server
    * [x] standard system utilities
* `Install the GRUB boot loader`
  * Force GRUB installation to the EFI removable media path: `Yes`
  * Update NVRAM variables to automatically boot into Debian: `Yes`
  * Run os-prober automatically to detect and boot other OSes: `No`
* `Finish the installation`
  * Is the system clock set to UTC: `Yes`
  * Reboot

**Finished**

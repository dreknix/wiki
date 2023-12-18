# btrfs

[https://btrfs.readthedocs.io/](https://btrfs.readthedocs.io/){target="_blank"}

## Layout

* https://sysguides.com/install-fedora-with-snapshot-and-rollback-support/
* https://youtu.be/JvfCieWkXxI?si=tFSJziZylZpbDwg0

With the following layout booting a read-only snapshot is possible since all
directories that needs to be writable are not part of the root file system
subvolume.

Example of `/etc/fstab`:

``` plaintext
UUID=...   /                          btrfs   noatime,compress=zstd:3,subvol=@                          0   0
UUID=...   /.snapshots                btrfs   noatime,compress=zstd:3,subvol=@snapshots                 0   0
UUID=...   /home                      btrfs   noatime,compress=zstd:3,subvol=@home                      0   0
UUID=...   /var/cache                 btrfs   noatime,compress=zstd:3,subvol=@var_cache                 0   0
UUID=...   /var/crash                 btrfs   noatime,compress=zstd:3,subvol=@var_crash                 0   0
UUID=...   /var/log                   btrfs   noatime,compress=zstd:3,subvol=@var_log                   0   0
UUID=...   /var/lib/AccountsService   btrfs   noatime,compress=zstd:3,subvol=@var_lib_accountsservice   0   0
UUID=...   /var/lib/gdm3              btrfs   noatime,compress=zstd:3,subvol=@var_lib_gdm3              0   0

UUID=...   /.btrfs                    btrfs   noatime,compress=zstd:3,subvolid=5                        0   0
```

!!! info

    The directories `/.btrfs` and `/.snapshots` must be only accessible by the
    root user and the group sudo (for tab completion).

    ``` console
    sudo chown root:sudo /.btrfs /.snapshots
    sudo chmod 0750 /.btrfs /.snapshots
    ```

!!! warning

    Due to LUKS disk encryption the `/boot` directory is not part of the root
    file system subvolume. Do not delete any kernels that are needed to boot an
    old snapshot.

Depending on the usage of the computer, other subvolumes are also needed:

* Docker: `/var/lib/docker`
* podman: `/var/lib/container`
* libvirt: `/var/lib/libvirt`
* Other services: `/srv`

``` plaintext title="/etc/fstab"
UUID=...   /var/lib/docker            btrfs   noatime,compress=zstd:3,subvol=@var_lib_docker            0   0
UUID=...   /var/lib/container         btrfs   noatime,compress=zstd:3,subvol=@var_lib_container         0   0
UUID=...   /var/lib/libvirt           btrfs   noatime,compress=zstd:3,subvol=@var_lib_libvirt           0   0

UUID=...   /srv                       btrfs   noatime,compress=zstd:3,subvol=@srv                       0   0
```

## Snapshots

* https://youtu.be/_97JOyC1o2o?si=8W8XuKHa7o7yqSre

### Create Snapshots

Create a snapshot from a subvolume:

``` console
sudo btrfs subvolume snapshot /path/to/volume /path/to/snapshot
```

The snapshot is writable by default. A read-only snapshot can be created with
the additional `-r` option.

### Read-only Snapshots

``` console
$ sudo btrfs property get -t subvol /path/to/snapshot
ro=true
```

Make snapshot writeable:

``` console
sudo btrfs property set -ts /path/to/snapshot ro false
```

## Rollback

First boot to the last working snapshot. If you have `grub-btrfs` installed, you
can choose the snapshot from the boot menu. Or you can add the snapshot as
a kernel option.

Normally btrfs adds the following option the kernel (`update-grub2`):

``` plaintext
rootflags=subvol=@
```

To select a different snapshot change the option during boot (press `e`) to:

``` plaintext
rootflags=subvolid=<id>
```

Now the snapshot is used as rootfs. Due to the chosen layout even a read-only
snapshot can be booted.

After the system was successfully booted check if the right snapshot is mounted:

``` console
$ mount | grep " / "
/dev/mapper/vda3_crypt on / type btrfs (rw,noatime,compress=zstd:3,discard=async,space_cache=v2,subvolid=277,subvol=/@snapshots/rootfs.20231214T1820)
```

Even if the mount option is `rw` the snapshot can be still read-only.

Change into `/.btrfs` and create a read-only snapshot of the current system:

``` console
$ sudo btrfs subvolume snapshot -r @ @snapshots/broken-rootfs
Create a readonly snapshot of '@' in '@snapshots/broken-rootfs'
```

Delete to broken root file system:

``` console
$ sudo btrfs subvolume snapshot delete @
Delete subvolume (no-commit): '/.btrfs/@'
```

Create a writable snapshot of the current booted snapshot as the new rootfs:

``` console
$ sudo btrfs subvolume snapshot @snapshots/rootfs.20231214T1820 @
Create a snapshot of '@snapshots/rootfs.20231214T1820' in './@'
```

Reboot the system and check the root file system:

``` console
$ mount | grep " / "
/dev/mapper/vda3_crypt on / type btrfs (rw,noatime,compress=zstd:3,discard=async,space_cache=v2,subvolid=282,subvol=/@)
```

## Tools

### Overview

* [:fontawesome-brands-github: linuxmint/timeshift](
   https://github.com/linuxmint/timeshift){target="_blank"}<br />
  A snapshot and restore tool that is maintained by the Linux Mint project. The
  tool requires a flat subvolume layout and a fixed naming scheme with `@` for
  root file system and `@home` for the folder `/home`.
* [:fontawesome-brands-github: openSUSE/snapper](
   https://github.com/openSUSE/snapper){target="_blank"}<br />
  A snapshot and restore tool that is maintained by openSUSE. The tool creates a
  `.snapshot` subvolume for each volume to store there the snapshots.
* [:fontawesome-brands-github: digint/btrbk](
   https://github.com/digint/btrbk){target="_blank"}<br />
  A simple perl script that can be started by crontab or systemd timers. The
  path of snapshots can be configured. The tool supports also backup via SSH to
  remote servers.
* [:fontawesome-brands-github: Antynea/grub-btrfs](
   https://github.com/Antynea/grub-btrfs){target="_blank"}<br />
  Include btrfs snapshots as boot options in the Grub menu

### btrbk

#### Config

The content of `/etc/btrbk/btrbk.conf`:

``` plaintext
# Enable transaction log
transaction_log            /var/log/btrbk.log

# Preserve all snapshots for a minimum period of time.
snapshot_preserve_min      1d

# Retention policy for the source snapshots.
snapshot_preserve          3h 5d 4w 6m 1y

snapshot_dir   /.snapshots
subvolume      /
snapshot_name  rootfs
subvolume      /home
snapshot_name  home
```

#### Create a snapshot

``` console
sudo btrbk run
```

To perform a try-run add the option `-n`.

#### Show diff between two Snapshots

Create a snapshot and install the package `htop` and create a second snapshot.
After that the differences between both snapshots can be shown:

``` console
$ sudo btrbk diff /.snapshots/rootfs.20231214T1820_1 /.snapshots/rootfs.20231214T1821
FLAGS  COUNT  SIZE        FILE
+c.        1   44.00 KiB  etc/mailcap
+c.        1  312.00 KiB  usr/bin/htop
+ci        1    2.49 KiB  usr/share/applications/htop.desktop
+c.        1   28.00 KiB  usr/share/applications/mimeinfo.cache
+ci        1    0.22 KiB  usr/share/doc/htop/AUTHORS
+..        1    4.00 KiB  usr/share/doc/htop/README.gz
+..        1    8.00 KiB  usr/share/doc/htop/changelog.Debian.gz
+..        1   20.00 KiB  usr/share/doc/htop/changelog.gz
+ci        1    1.29 KiB  usr/share/doc/htop/copyright
+c.        1   28.00 KiB  usr/share/icons/hicolor/icon-theme.cache
+c.        1   12.00 KiB  usr/share/icons/hicolor/scalable/apps/htop.svg
+..        1    8.00 KiB  usr/share/man/man1/htop.1.gz
+..        1    4.00 KiB  usr/share/pixmaps/htop.png
+c.        1  140.00 KiB  var/lib/apt/extended_states
+ci        1    0.56 KiB  var/lib/dpkg/info/htop.list
+ci        1    0.63 KiB  var/lib/dpkg/info/htop.md5sums
+c.        1    2.30 MiB  var/lib/dpkg/status
+c.        1    2.30 MiB  var/lib/dpkg/status-old
```

The list of available snapshots can be shown with `btrbk list snapshots`.

### compsize

``` console
sudo apt install btrfs-compsize
```

To see the impact of the used compression:

``` console
$ sudo compsize -x /
Processed 275808 files, 182511 regular extents (183978 refs), 91269 inline.
Type       Perc     Disk Usage   Uncompressed Referenced
TOTAL       79%      7.0G         8.8G         9.5G
none       100%      6.0G         6.0G         6.5G
zstd        33%      961M         2.7G         2.9G
```

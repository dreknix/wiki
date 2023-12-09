# btrfs

[https://btrfs.readthedocs.io/](https://btrfs.readthedocs.io/){target="_blank"}

## Layout

* https://sysguides.com/install-fedora-with-snapshot-and-rollback-support/
* https://youtu.be/JvfCieWkXxI?si=tFSJziZylZpbDwg0

## Snapshots

* https://youtu.be/_97JOyC1o2o?si=8W8XuKHa7o7yqSre

### Read-only Snapshots

``` console
sudo btrfs property get -t subvol /path/to/snapshot
```

Make snapshot writeable:

``` console
sudo btrfs property set -ts /path/to/snapshot ro false
```

## Tools

### Overview

* [:fontawesome-brands-github: linuxmint/timeshift](
   https://github.com/linuxmint/timeshift){target="_blank"}<br />
  A snapshot and restore tool that is maintained by the Linux Mint project. The
  tool requires a flat subvolume layout and a fixed naming scheme with `@` for
  root filesystem and `@home` for the folder `/home`.
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

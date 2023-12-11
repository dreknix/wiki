---
date: 2023-12-11
categories:
  - Tools
  - UNIX
---

# zram and Swap

The zram kernel module creates compressed RAM-based block devices named
`/dev/zramX`. These zram devices allow fast I/O and the compression provides
RAM space savings.

<!-- more -->

## Install zram

Install zram in Debian:

``` console
sudo apt install zram-tools
```

In order to enable zram, the computer needs to be rebooted.

## Configure zram

The status of the zram module can be queried with `systemctl status zramswap.service`.
The configuration and usage can be shown with `zramctl`:

``` console
$ zramctl
NAME       ALGORITHM DISKSIZE DATA COMPR TOTAL STREAMS MOUNTPOINT
/dev/zram0 lz4           256M   4K   63B   20K       8 [SWAP]
```

Change the configuration parameters by editing the file `/etc/default/zramswap`:

```
ALGO=zstd
PERCENT=25
```

The will change the default compression algorithm from `lz4` to `zstd` and the
default size from `256 MiB` to `25 %` of the total RAM.

``` console
$ zramctl
NAME       ALGORITHM DISKSIZE DATA COMPR TOTAL STREAMS MOUNTPOINT
/dev/zram0 zstd          7.8G   4K   59B   20K       8 [SWAP]
```

If the computer has a configured swap file or swap partition, the zram swap gets
a higher priority (`100`) instead of the default of `-2`:

``` console
$ swapon
NAME       TYPE      SIZE USED PRIO
/dev/dm-2  partition   4G   0B   -2
/dev/zram0 partition 7.8G   0B  100
```

When the computer uses suspend to disk, a swap file or swap partition is needed
besides zram.

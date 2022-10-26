# Advanced Package Tool (apt)

* [debian APT Git-Repository](https://salsa.debian.org/apt-team)
* [apt(8)](https://manpages.debian.org/apt/apt.8.en.html)
* [apt-cache(8)](https://manpages.debian.org/apt/apt-cache.8.en.html)
* [apt-mark(8)](https://manpages.debian.org/apt/apt-mark.8.en.html)

## Keys

### List Keys

Get a list of all keys configured for `apt` on your system:

```console
$ sudo apt-key list
```

### Refresh Expired Keys

When you get an `EXPKEYSIG` error while `apt update`, you can update the expired
key with the following command:

```console
$ sudo apt-key adv --keyserver keys.gnupg.net --recv-keys AAAABBBBCCCCDDDD
```

## Packages have been kept back

```console
$ sudo apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  qemu-guest-agent snapd
0 upgraded, 0 newly installed, 0 to remove and 2 not upgraded.
```

If packages are kept back, there are three reasons for that:

* The package is marked as hold back
* The package has changed dependencies
* The package is a phased update

### Packages are marked as hold back

Individual packages can be marked as held back with `apt-mark hold`. This
will prevent that the packages are automatically installed, upgraded or removed.

To check if a package is held back use the command `apt-mark unhold`:

```console
$ apt-mark showhold
qemu-guest-agent
```

### Packages have changed dependencies

If the dependencies of a package has changed, and one of the new dependencies
is not unmet, than the package will not be updated.

To see which package version is installed and which is upgradable use the
command `apt list -a`:

```console
$ apt list -a qemu-guest-agent
Listing... Done
qemu-guest-agent/jammy-updates 1:6.2+dfsg-2ubuntu6.5 amd64 [upgradable from: 1:6.2+dfsg-2ubuntu6.4]
qemu-guest-agent/now 1:6.2+dfsg-2ubuntu6.4 amd64 [installed,upgradable to: 1:6.2+dfsg-2ubuntu6.5]
qemu-guest-agent/jammy-security 1:6.2+dfsg-2ubuntu6.2 amd64
qemu-guest-agent/jammy 1:6.2+dfsg-2ubuntu6 amd64
```

Use `apt-show` for each version to check the dependencies:

```console
$ apt show qemu-guest-agent=1:6.2+dfsg-2ubuntu6.4
$ apt show qemu-guest-agent=1:6.2+dfsg-2ubuntu6.5
```

If there are changed dependencies, install the update with the new depencies
via:

```console
$ sudo apt --with-new-pkgs upgrade
```

When you use `apt install` to resolve this issue the packages will be marked as
manual installed.

### Packages are a phased update

Sometimes packages are not available to all users. Then only some users receive
the update. This is a safety feature.

To check whether an update is a phased update, use the following command:

```console
$ apt-cache policy qemu-guest-agent
qemu-guest-agent:
  Installed: 1:6.2+dfsg-2ubuntu6.4
  Candidate: 1:6.2+dfsg-2ubuntu6.5
  Version table:
     1:6.2+dfsg-2ubuntu6.5 500 (phased 30%)
        500 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages
 *** 1:6.2+dfsg-2ubuntu6.4 100
        100 /var/lib/dpkg/status
     1:6.2+dfsg-2ubuntu6.2 500
        500 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages
     1:6.2+dfsg-2ubuntu6 500
        500 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages
```

In this case the update is only available for 30% of all users.

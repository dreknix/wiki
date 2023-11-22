---
date: 2023-11-22
categories:
  - Tools
  - UNIX
---

# Using SPICE with TrueNAS Scale Cobia (23.10)

TrueNAS Scale Cobia (23.10) disabled the option to access VM displays via VNC.
The connection method is changed to
[SPICE](https://www.spice-space.org/){target="_blank"}.

<!-- more -->

There are many different clients, which support Spice. Under Debian the tool
[:fontawesome-brands-gitlab: Remmina](https://gitlab.com/Remmina/Remmina){target="_blank"}
is available that is a GTK-based remote desktop client. This tool supports many
different protocols: RDP, VNC, SPICE, X2Go, SSH, etc. Some of the protocols,
i.e. SPICE, are only available via additional plugins.

To install Remina with the SPICE plugin (the RDP and VNC plugins are installed
by default):

``` console
sudo apt install remmina remmina-plugin-spice
```

When Remina is installed, you can connect to the server via the SPICE protocol:

``` console
remmina spice://server.example.org
```

If multiple VMs are running the different ports can be found in the details of
the display device.

!!! tip

    If a VM was created before version 23.10 the display of the VM must be
    deleted and newly created. Since version 23.10 only the SPICE protocol is
    available and password, which protects the connection, must be set.

    Sometimes the VM must use UEFI in order to work properly with the SPICE
    protocol.

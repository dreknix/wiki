# Terminal Access

## Connect to serial console

Check if terminal is available in guest:

```console
$ qm monitor 1500
Entering Qemu Monitor for VM 1500 - type 'help' for help
qm> info chardev
...
serial0: filename=disconnected:unix:/var/run/qemu-server/1500.serial0,server=on
qm>
```

Starting a connection to serial console:

```console
$ qm terminal 1500
starting serial terminal on interface serial0 (press control-O to exit)
```

Stop the connection with ++Ctrl+o++.

## Switch to console terminal via QEMU

To switch to a virtual console, you can:
* Go to `Monitor` of the virtual machine in the Web front-end and
  use the command `sendkey ctrl-alt-fN`.
* Use `qm sendkey vmid "sendkey-ctrl-alt-fN"` in the shell.

* ++ctrl+alt+f1++ - graphical login screen
* ++ctrl+alt+f2++ - graphical desktop
* ++ctrl+alt+f3++ - console 3
* ...
* ++ctrl+alt+f6++ - console 6

## Switch to console terminal via `chvt`

The command `chvt N` switch to the virtual console `N`. For example:

```console
$ sudu chvt 3
```

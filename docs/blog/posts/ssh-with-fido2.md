---
date: 2024-02-23
categories:
  - Tools
  - UNIX
---

# Using OpenSSH with FIDO2 Hardware Keys

OpenSSH supports FIDO2 hardware keys for additional protection. The private key
is securely stored on hardware. The file in the `~/.ssh/` directory contains
only the credential id with other non-sensitive meta-data.

<!-- more -->

## Configure the Hardware Key

First install the tool [fido2-token(1)](
https://manpages.debian.org/fido2-token.1.en.html){target="_blank"}:

``` console
sudo apt install fido2-tools
```

Get list of all available hardware keys:

``` console
$ fido2-token -L
/dev/hidraw3: vendor=0x1050, product=0x0407 (Yubico YubiKey OTP+FIDO+CCID)
```

Get information about a specific hardware key:

``` console
$ fido2-token -I /dev/hidraw3
proto: 0x02
major: 0x05
minor: 0x04
build: 0x03
caps: 0x05 (wink, cbor, msg)
version strings: U2F_V2, FIDO_2_0, FIDO_2_1_PRE
extension strings: credProtect, hmac-secret
transport strings: nfc, usb
algorithms: es256 (public-key), eddsa (public-key)
aaguid: 2fc0579f811347eab116bb5a8db9202a
options: rk, up, noplat, clientPin, credentialMgmtPreview
maxmsgsiz: 1200
maxcredcntlst: 8
maxcredlen: 128
fwversion: 0x50403
pin protocols: 2, 1
pin retries: 8
uv retries: undefined
```

Some of the features of FIDO2 (i.e. resident keys) requires a PIN protection of
FIDO2. To set a PIN for first time:

``` console
fido2-token -S /dev/hidraw3
```

Or change the current PIN:

``` console
fido2-token -C /dev/hidraw3
```

If the PIN gets lost a factory reset is needed.

!!! warning

    After a factory reset all keys that are created by this hardware key can not
    be used anymore.

The factory reset can only be performed in the first five seconds after the
hardware key is plugged in:

``` console
fido2-token -R /dev/hidraw3
```

## Create OpenSSH Keys

When creating keys with a FIDO2 hardware key two options exist. Resident or
non-resident keys. In the case of non-resident keys the private key file is
needed besides the hardware key and offers security even the hardware key is
lost or stolen. A resident key has the advantage that moving to another client
machine is simple, since the key file can be generated with the hardware key
and the PIN.

!!! warning

    You should configure at least two hardware keys so that in case of a
    hardware failure or the key is lost a login into the remote machine is still
    possible.

### Non-Resident Keys

Create a non-resident key with [ssh-keygen(1)](
https://manpages.debian.org/ssh-keygen.1.en.html){target="_blank"}:

``` console
ssh-keygen -t ed25519-sk -f ~/.ssh/id_dreknix_fido2_a -C "dreknix fido2 a"
```

The suffix `-sk` identicates that a secure key is used. Optional the key type
`ecdsa-sk` can be used, when an older hardware key is used that does not support
the prefered `ed25519-sk` key type.

This key was called `a` to emphasize that more than one hardware key should be
configured.

For using this SSH key, the file `~/.ssh/id_dreknix_fido2_a` and the hardware
key is needed. When using the key the passphrase (optional) must be provided and
the key needs to be touched.

!!! info

    If touching the key is not feasible for a specific szenario the key can be
    created with the option `-O no-touch-required`. In the file
    `~/.ssh/authorized_keys` the public key must have the option
    `no-touch-required` in the beginning of the line, see [authorized_keys(5)](
    https://manpages.debian.org/authorized_keys.5.en.html){target="_blank"}.

To increase security the key can require the PIN when used. In order to create
such a key, the option `-O verification-required` can be used:

``` console
ssh-keygen -t ed25519-sk \
           -f ~/.ssh/id_dreknix_fido2_a \
           -O verification-required \
           -C "dreknix fido2 a"
```

Now every time the key is used, the PIN of the hardware key needs to be
provided.

!!! info

    This kind of key can not be stored by an agent. So the option `-o IdentyAgent=none`
    may be required by `ssh`.

The SSH daemon can be configured to only accept PIN protected keys with the
option `PubkeyAuthOptions verify-required` in the `/etc/ssh/sshd_config` file.
The public key can also by marked with the option `verify-required`.

### Resident Keys

To store the private key on the hardware key the option `-O resident` must be
used when creating the key:

``` console
ssh-keygen -t ed25519-sk \
           -f ~/.ssh/id_dreknix_fido2_a \
           -O verification-required \
           -O resident \
           -O application=ssh:dreknix_fido2_a
           -C "dreknix fido2 a"
```

The option `-O application=ssh:xxx` is a comment that is helpful with the key
management, when more than one key are stored. All other option of the
non-resident keys can be uses also with resident keys.

To see which keys are stored:

``` console
$ fido2-token -L -r /dev/hidraw3
Enter PIN for /dev/hidraw3:
00: yIxr1XK+loMkBeD0mZe1RQWNB321S5MvvbRRPfG/nxY= ssh:dreknix_fido2_a
```

Each hardware device can store only a limited amount of keys:

``` console
$ fido2-token -I -c /dev/hidraw3
Enter PIN for /dev/hidraw3:
existing rk(s): 1
remaining rk(s): 24
```

In this case the total of 25 resident keys can be stored on this device.

When the key file is deleted or the hardware key is used on another computer,
the key file can be created with:

``` console
$ ssh-keygen -K
Enter PIN for authenticator:
You may need to touch your authenticator to authorize key download.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Saved ED25519-SK key ssh:dreknix_fido2_a to id_ed25519_sk_rk_dreknix_fido2_a
$ ls -l id_ed25519_sk_rk_*
-rw------- 1 dreknix dreknix 505 Feb 10 10:00 id_ed25519_sk_rk_dreknix_fido2_a
-rw-r--r-- 1 dreknix dreknix 168 Feb 10 10:00 id_ed25519_sk_rk_dreknix_fido2_a.pub
```

The name of the files is taken from the option `application=ssh:xxx`. Therefore,
this option is very important, when handling multiple keys. If more than one
hardware key is present, `ssh-keygen -K` extract the all keys from the device
that is touched first.

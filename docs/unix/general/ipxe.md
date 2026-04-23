# iPXE

[iPXE](https://ipxe.org/) is a open source boot firmware, that supports booting
from a web server via HTTP.

## Configure and create iPXE boot iso

Get the source code of iPXE:

``` console
git clone https://git.ipxe.org/ipxe.git
```

Configure iPXE:

* in `config/general.h`
  * ~~enable `DOWNLOAD_PROTO_HTTPS`~~ - *already enabled*
  * enable `NSLOOKUP_CMD`
  * enable `PING_CMD`
  * ~~enable `CONSOLE_CMD`~~ - *already enabled*
* in `config/console.h`
  * ~~enable `CONSOLE_FRAMEBUFFER`~~ - *already enabled*
  * define `KEYBOARD_MAP` to `de`

Create a script that will be embedded into the iPXE binary:

``` ipxe
#!ipxe

# config

echo
echo iPXE embeded script
echo

# needed since embed script: https://ipxe.org/embed
dhcp || echo DHCP failed
sleep 2

echo
echo iPXE Settings
echo
show uuid ||
echo
show mac ||
show ip ||
show netmask ||
show gateway ||
show dns ||
show domain ||
echo
show platform ||
sleep 2

:retry
echo
echo iPXE: Chain loading http://${next-server}/ipxe/boot.ipxe
echo
sleep 2
chain --replace --autofree http://${next-server}/ipxe/boot.ipxe || echo Chain loading failed
shell
goto retry
```

Build UEFI:

``` console
make bin-x86_64-efi/ipxe.efi EMBED=script.ipxe
```

To chainload a signed iPXE, a Microsoft-signed shim is needed. The shim can load
than a MOK self-signed (Machine Owner Key) iPXE loader.

Get Microsoft-signed shim:

``` console
apt install shim-signed
```

To create a signed version of the UEFI, we need to crate a MOK key pair:

``` console
openssl req -newkey rsa:2048 -nodes -keyout MOK.key \
            -new -x509 -sha256 -days 3650 \
            -subj "/CN=QEMU MOK/" -out MOK.crt
```

Create a DER-encoded cert for enrollment in shim:

``` console
openssl x509 -in MOK.crt -outform DER -out MOK.cer
```

Sign the iPXE binary:

``` console
$ sbsign --key MOK.key --cert MOK.crt --output ipxe-signed.efi bin-x86_64-efi/ipxe.efi
Signing Unsigned original image
```

Verify the signature (`apt install sbsigntool`):

``` console
$ sbverify --cert MOK.crt ipxe-signed.efi
Signature verification OK
```

Create boot ISO image:

``` console
dd if=/dev/zero of=efi.iso bs=1M count=64

mkfs.fat -F32 efi.iso

mkdir ./efi

mount -o loop efi.iso ./efi

mkdir -p ./efi/EFI/BOOT
sudo cp /usr/lib/shim/shimx64.efi.signed  ./efi/EFI/BOOT/BOOTX64.EFI
sudo cp /usr/lib/shim/mmx64.efi.signed    ./efi/EFI/BOOT/mmx64.efi
sudo cp ipxe-signed.efi                   ./efi/EFI/BOOT/grubx64.efi
sudo cp MOK.cer                           ./efi/MOK.cer

umount ./efi
```

Test if `BOOTX64.EFI` and `mmx64.efi` (MOK manager) are signed by Microsoft and
Debian:

``` console
$ sbverify --list /usr/lib/shim/mmx64.efi.signed
warning: data remaining[732096 vs 850176]: gaps between PE/COFF sections?
signature 1
image signature issuers:
 - /CN=Debian Secure Boot CA
image signature certificates:
 - subject: /CN=Debian Secure Boot Signer 2022 - shim
   issuer:  /CN=Debian Secure Boot CA
```

## Test with QEMU

Create writeable image for UEFI variables:

``` console
cp /usr/share/OVMF/OVMF_VARS_4M.ms.fd ./ovmf-vars.fd
```

Start the virtual machine:

``` console
qemu-system-x86_64 \
    -machine q35,smm=on \
    -drive if=pflash,format=raw,readonly=on,file=/usr/share/OVMF/OVMF_CODE_4M.secboot.fd\
    -drive if=pflash,format=raw,file=./ovmf-vars.fd\
    -global driver=cfi.pflash01,property=secure,value=on\
    -m 4096 -smp 4 \
    -cdrom ./efi.iso -boot d -net nic -net user
```

For testing purposes the following flags might be helpful:

* `-boot menu=on` - add some time to press `F2`
* `--nographic` - boot in only text modus
* `-monitor stdio` - then you can `sendkey ctrl-alt-f2` to switch the virtual
  ttys in a Linux guest

In order to leave the terminal mode press `Ctrl-a + x`.

Check which certificates are already enrolled and if secure boot is enabled
(`apt install python3-virt-firmware`):

``` console
$ virt-fw-vars --input /usr/share/OVMF/OVMF_VARS_4M.ms.fd --print --verbose
INFO: reading raw edk2 varstore from /usr/share/OVMF/OVMF_VARS_4M.ms.fd
INFO: var store range: 0x64 -> 0x40000
...
name=KEK guid=guid:EfiGlobalVariable size=2565 time=2026-01-04 18:19:19+00:00
  siglist type=guid:EfiCertX509 count=1
    subject CN=Debian UEFI Secure Boot (PK/KEK key)
    issuer CN=Debian UEFI Secure Boot (PK/KEK key)
  siglist type=guid:EfiCertX509 count=1
    subject CN=Microsoft Corporation KEK CA 2011
    issuer CN=Microsoft Corporation Third Party Marketplace Root
...
name=PK guid=guid:EfiGlobalVariable size=1005 time=2026-01-04 18:19:20+00:00
  siglist type=guid:EfiCertX509 count=1
    subject CN=Debian UEFI Secure Boot (PK/KEK key)
    issuer CN=Debian UEFI Secure Boot (PK/KEK key)
...
name=SecureBootEnable guid=guid:EfiSecureBootEnableDisable size=1
  bool: ON
...
name=db guid=guid:EfiImageSecurityDatabase size=3143 time=2026-01-04 18:19:19+00:00
  siglist type=guid:EfiCertX509 count=1
    subject CN=Microsoft Windows Production PCA 2011
    issuer CN=Microsoft Root Certificate Authority 2010
  siglist type=guid:EfiCertX509 count=1
    subject CN=Microsoft Corporation UEFI CA 2011
    issuer CN=Microsoft Corporation Third Party Marketplace Root
...
```

The file `MOK.cer` can also be added to the UEFI variables:

``` console
virt-fw-vars --input ./ovmf-vars.fd \
             --output ./ovmf-vars.fd \
             --add-mok $(uuidgen) MOK.cer
```

## Boot examples

### Debian Network Install

Boot Debian image for network install with secure boot enabled and preseeding
the automatic installation.

``` plain
set debian-boot-url ${debian-url}/${debian-version}/netboot

kernel ${debian-boot-url}/linux \
       debian-installer/locale=en_US.UTF-8 keyboard-configuration/xkb-keymap=de \
       hw-detect/load_firmware=false netcfg/choose_interface=auto \
       netcfg/get_hostname=${hostname} netcfg/get_domain=${domain} \
       preseed/url=${debian-url}/d-i/${debian-version}/preseed.cfg \
       || goto failed
initrd ${debian-boot-url}/initrd.gz || failed
shim ${debian-boot-url}/bootnetx64.efi || failed

boot || goto failed
```

For debugging add `DEBCONF_DEBUG=5` as kernel parameter and view the logs on
terminal `tty4`. The used kernel parameters are necessary, since these must be
set before preseeding via network is possible.

### Ubuntu Server Install

Boot Ubuntu server image with secure boot enabled and preseeding via Subiquity.

``` plain
set ubuntu-boot-url ${ubuntu-url}/${ubuntu-version}
set subiquity-url ${ubuntu-url}/subiquity

kernel ${ubuntu-boot-url}/server/vmlinuz \
       splash root=/dev/ram0 ramdisk_size=1500000 \
       cloud-config-url=/dev/null \
       ip=dhcp url=${iso-url}/ubuntu-${ubuntu-version-number}-live-server-amd64.iso \
       autoinstall \
       ds=nocloud-net;s=${subiquity-url}/ \
       cc:autoinstall: { user-data: { hostname: ${hostname} } } end_cc \
       cc:autoinstall: { user-data: { fqdn: ${hostname}.${domain} } } end_cc \
       || goto failed
initrd ${ubuntu-boot-url}/server/initrd || goto failed
shim ${ubuntu-boot-url}/server/bootx64.efi || goto failed

boot || goto failed
```

The files `meta-data`, `network-config`, and `vendor-data` can be empty.

Example of `user-data`:

``` plain
#cloud-config
autoinstall:
  # version is an Autoinstall required field.
  version: 1
  keyboard:
    layout: 'de'
    variant: 'nodeadkeys'
  locale: 'en_US.UTF-8'
  timezone: 'Europe/Berlin'
  # Subiquity will, by default, configure a partition layout using LVM.
  # The 'direct' layout method shown here will produce a non-LVM result.
  storage:
    layout:
      name: 'direct'
  # Any desired additional packages may also be listed here.
  packages:
    - htop
  updates: all
  shutdown: reboot
  ssh:
    install-server: true
    allow-pw: false
    authorized-keys:
      - '{{ SSH PUBLIC KEY }}'
  # A postinstall script may optionally be used for further install
  # customization. Deploy the script on the webserver.
  # late-commands:
  #   - wget -O /target/postinstall.sh {{ subiquity_postinstall_url }}
  #   - curtin in-target -- bash /postinstall.sh
  #   - rm -f /target/postinstall.sh
  # Additional cloud-init configuration affecting the target
  # system can be supplied underneath a user-data section inside of
  # autoinstall.
  user-data:
    # must have a hostname from DHCP (pool ip is not working)
    # hostname: set in kernel-cmdline (in ipxe)
    # fqdn: set in kernel-cmdline (in ipxe)
    manage_etc_hosts: true
    disable_root: false
    chpasswd:
      expire: false
    system_info:
      default_user:
        name: 'root'
        lock_passwd: false
        hashed_passwd: '{{ HASHED PASSWORD }}'
```

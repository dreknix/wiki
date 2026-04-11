# Booting

## Secure Boot

Check if secure boot is enabled:

``` console
$ sudo mokutil --sb-state
SecureBoot disabled
Platform is in Setup Mode
```

List the enrolled certificates:

``` console
$ sudo mokutil --list-enrolled
...
```

If you find a module verification error in `dmesg` that tainting the kernel:

``` plain
[   12.141441] nvidia: module verification failed: signature and/or required key missing - tainting kernel
```

You need to add the key `/var/lib/dkms/mok.pub` to the enrolled keys.

View the certificate that is used to sign modules build with the DKMS:

``` console
$ openssl x509 -in /var/lib/dkms/mok.pub -noout -text
...
```

Compare this certificate with the enrolled certificates. If the certificate is
missing start enrolling it on the host:

``` console
$ mokutil --import /var/lib/dkms/mok.pub
input password:
input password again:
```

After reboot the MOKMananger EFI binary is started, where the certificate can be
enrolled. You need to enter the password again. Afterwards, check the enrolled
certificates.

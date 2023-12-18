---
tags:
  - draft
---

# libvirt

## TPM

See: https://en.opensuse.org/Software_TPM_Emulator_For_QEMU

``` console
virsh domcapabilities --machine pc-q35-6.2 | xmllint --xpath '/domainCapabilities/os' -
```

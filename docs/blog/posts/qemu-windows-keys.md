---
date: 2023-12-04
categories:
  - Tools
  - Windows
---

# QEMU and Windows Keys

OEM Windows keys are often stored in the software licensing (SLIC) or Microsoft
data management (MSDM) table. When a key is stored the unattended installation
can use a generic key and the stored key is than used and activated. The usage
of such a key can be tested with QEMU/libvirt.

<!-- more -->

The SLIC or MSDM table can be read via the Advanced Configuration and Power
Interface (ACPI) in Linux. To read the Windows license key the following command
can be used:

``` console
egrep -ao '[[:alnum:]-]{29}' /sys/firmware/acpi/tables/MSDM
```

Under Windows the following PowerShell snippet can be used to extract the
license key:

``` powershell
Get-CimInstance -query 'select * from SoftwareLicensingService'
```

The key is stored in the `OA3xOriginalProductKey` attribute.

The table file needs to be stored in `/usr/share/seabios/` due to AppArmor. If
the file is not stored in this directory, a permission denied error is thrown.
The MSDM (or SLIC) table is included by using additional QEMU parameters:

``` xml
<qemu:commandline>
  <qemu:arg value='-acpitable'/>
  <qemu:arg value='file=/usr/share/seabios/MSDM'/>
</qemu:commandline>
```

In order to "simulate" the OEM hardware the system management BIOS (SMBIOS) can
be used:

``` xml
<qemu:commandline>
<qemu:arg value='-smbios'/>
<qemu:arg value='type=0,version=??,date=??'/>
<qemu:arg value='-smbios'/>
<qemu:arg value='type=1,manufacturer=??,product=??,version=??,serial=??,uuid=??,sku=??,family=??'/>
```

The concrete values can be extracted with the `dmidecode` tool:

``` console
dmidecode -t 0
dmidecode -t 1
```

The embedded license key can be activated with the following commands. First
the key is read from the SLIC or MSDM table and than stored in Windows. The
last command activates the license key:

``` powershell
$key = (Get-CimInstance -query 'select * from SoftwareLicensingService').OA3xOriginalProductKey
cscript.exe "$env:SystemRoot\System32\slmgr.vbs" /ipk "$key"
cscript.exe "$env:SystemRoot\System32\slmgr.vbs" /ato
```

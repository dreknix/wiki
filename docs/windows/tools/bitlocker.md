# BitLocker

BitLocker is a proprietary volume encryption tool includes in Microsoft Windows.
Without any configuration BitLocker using TPM. When using TPM on start of the
computer no password needs to be entered.

The command line command
[manage-bde](https://learn.microsoft.com/windows-server/administration/windows-commands/manage-bde){target="_blank"}
can be used to turn on BitLocker and monitor the current status.

## Configure

BitLocker can be configured through group policies or directly with registry
keys.

!!! info

    This configuration settings requires at least Windows 10, older versions
    of Windows use different options.

### Group  Policies

The group policies are changed in the domain controller or locally via
`gpedit.msc`.

#### Use without TPM

To enable password protected volume encryption the following group policy needs
to be enabled (see
[admx.help](https://admx.help/?Category=Windows_11_2022&Policy=Microsoft.Policies.VolumeEncryption::ConfigureAdvancedStartup_Name){target="_blank"}):

* `Computers Configuration`
  * `Policies` > `Administrative Templates` > `Windows Components`
    * `BitLocker Drive Encryption`
      * `Operating System Drives`
        * `Require additional authentication at startup`: `Enable`
          * `Allow BitLocker without a compatible TPM (requires a password or a startup key on a USB flash drive)`: `Checked`
          * `Configure TPM startup`: `Do not allow TPM`
          * `Configure TPM startup PIN` : `Do not allow startup PIN with TPM`
          * `Configure TPM startup key`: `Do not allow startup key with TPM`
          * `Configure TPM startup key and PIN`: `Do not allow startup key and PIN with TPM`

#### Enable better encryption

To use 256-bit AES encryption instead of the 128-bit default, the following
group policies must be defined (see
[admx.help](https://admx.help/?Category=Windows_11_2022&Policy=Microsoft.Policies.VolumeEncryption::EncryptionMethodWithXts_Name){target="_blank"}):

* `Computers Configuration`
  * `Policies` > `Administrative Templates` > `Windows Components`
    * `BitLocker Drive Encryption`
      * `Choose drive encryption method and cipher strength (Windows 10 [Version 1511] and later)`
        * `Select the encryption method for operating system drives`: `XTS-AES 256-bit`
        * `Select the encryption method for fixed data drives`: `XTS-AES 256-bit`
        * `Select the encryption method for removable data drives`: `XTS-AES 256-bit`

### Registry Settings

#### Use without TPM

To enable password protected volume encryption the following registry entries
need to be defined:

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FVE`

* `UseAdvancedStartup`: `DWORD:1`
* `EnableBDEWithNoTPM`: `DWORD:1`
* `UseTPM`: `DWORD:0`
* `UseTPMPIN`: `DWORD:0`
* `UseTPMKey`: `DWORD:0`
* `UseTPMKeyPIN`: `DWORD:0`

#### Enable better encryption

To use 256-bit AES encryption instead of the 128-bit default, the following
registry entries must be defined:

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FVE`

* `EncryptionMethodWithXtsOs`: `DWORD:7`
* `EncryptionMethodWithXtsFdv`: `DWORD:7`
* `EncryptionMethodWithXtsRdv`: `DWORD:4`

## Enable encryption

Enable BitLocker with a password (must be at least 8 characters):

``` powershell
PS> manage-bde.exe -on C: -password
```

Using abbreviations:

``` powershell
PS> manage-bde.exe -on C: -pw
```

To enable the encryption, the computer needs to be restarted: `shutdown /r /t
0`.

!!! warning

    The keyboard layout can be different during password prompt of BitLocker.
    Therefore be prepared, if the password is misspelled that you used a
    key that has a different mapping.

!!! info

    If the keyboard layout is different or you misspelled the password press
    ++ESC++ to boot without BitLocker enabled. The password needs to be set
    again.

## Check status

After first successful restart, the encryption of the volume is starting. The
current progress can be monitored with:

``` powershell
PS> manage-bde.exe -status C:
BitLocker Drive Encryption: Configuration Tool version 10.0.22621
Copyright (C) 2013 Microsoft Corporation. All rights reserved.

Volume C: [Windows]
[OS Volume]

    Size:                 69,13 GB
    BitLocker Version:    2.0
    Conversion Status:    Used Space Only Encrypted
    Percentage Encrypted: 100,0%
    Encryption Method:    AES 256
    Protection Status:    Protection On
    Lock Status:          Unlocked
    Identification Field: Unknown
    Key Protectors:
        Password
```

## Add Numerical Password (as Fallback)

The numerical password is enabled with the following command:

``` powershell
PS> manage-pde -protectors -add C: -recoverypassword
```

or by using abbreviations:

``` powershell
PS> manage-bde.exe -protectors -add C: -rp
```


To get the protector methods:

``` powershell
PS> manage-bde.exe -protectors -get C:
BitLocker Drive Encryption: Configuration Tool version 10.0.22621
Copyright (C) 2013 Microsoft Corporation. All rights reserved.

Volume C: [Windows]
All Key Protectors

    Numerical Password:
      ID: {89701158-94B4-42D2-87EB-82B8A03E45CD}
      Password:
        463881-116138-212971-219626-306295-227139-146784-038038

    Password:
      ID: {B1129007-8A8C-4398-BC67-0F608F771ABD}

```

The `RecoveryPassword` is called numerical password and the value is visible.
Please store this recovery password in case you forget your password.

## Change Password

The password can only be changed if a secondary protector is enabled (i.e., a
numerical recovery password):

``` powershell
PS> manage-pde -changepassword C:
```

## Script

The following PowerShell script enable BitLocker on `C:`:

``` powerShell
if ((Get-BitLockerVolume C:).EncryptionMethod -ne "None") {
  Write-Host ""
  Write-Host "Volume C: is already encrypted" -ForegroundColor red
  Get-BitLockerVolume C: | Select-Object *
  Write-Host "Disable BitLocker: " -NoNewLine
  Write-Host "Disable-BitLocker -MountPoint C:" -ForegroundColor yellow
  Exit 1
}

function Set-RegistryProperty {
  Param (
    [Parameter(Mandatory=$True, Position=0)]
    [String] $Path,
    [Parameter(Mandatory=$True, Position=1)]
    [String] $Name,
    [Parameter(Mandatory=$True, Position=2)]
    [String] $Type,
    [Parameter(Mandatory=$True, Position=3)]
    [Object] $Value
  )

  if (-not (Test-Path $Path)) {
    New-Item -Path $Path -Force > $null
  }
  if ((Get-Item $Path).Property -contains $Name) {
    Set-ItemProperty -Path $Path -Name $Name `
                     -Type $Type -Value $Value -Force > $null
  } else {
    New-ItemProperty -Path $Path -Name $Name `
                     -PropertyType $Type -Value $Value -Force > $null
  }
}

Write-Host ""
Write-Host "Configure Bitlocker without TPM and better encryption"
$properties = `
  @{Name = 'UseAdvancedStartup';          Type = 'DWORD'; Value = 1}, `
  @{Name = 'EnableBDEWithNoTPM';          Type = 'DWORD'; Value = 1}, `
  @{Name = 'UseTPM';                      Type = 'DWORD'; Value = 0}, `
  @{Name = 'UseTPMPIN';                   Type = 'DWORD'; Value = 0}, `
  @{Name = 'UseTPMKey';                   Type = 'DWORD'; Value = 0}, `
  @{Name = 'UseTPMKeyPIN';                Type = 'DWORD'; Value = 0}, `
  @{Name = 'EncryptionMethodWithXtsOs';   Type = 'DWORD'; Value = 7}, `
  @{Name = 'EncryptionMethodWithXtsFdv';  Type = 'DWORD'; Value = 7}, `
  @{Name = 'EncryptionMethodWithXtsRdv';  Type = 'DWORD'; Value = 4}

ForEach ($property in $properties) {
  Set-RegistryProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\FVE' `
                       -Name $property.Name `
                       -Type $property.Type -Value $property.Value
}

Write-Host ""
Write-Host "Activate Bitlocker"
Enable-BitLocker -MountPoint C: -PasswordProtector
Enable-BitLocker -MountPoint C: -RecoveryPasswordProtector `
                                -ErrorAction SilentlyContinue

Write-Host ""
Write-Host "Reboot: " -NoNewLine
Write-Host "shutdown /r /t 0" -ForegroundColor yellow
Write-Host ""
```

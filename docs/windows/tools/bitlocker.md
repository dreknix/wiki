# BitLocker

BitLocker is a proprietary volume encryption tool includes in Microsoft Windows.
Without any configuration BitLocker using TPM. When using TPM on start of the
computer no password needs to be entered.

## Use without TPM

To enable password protected volume encryption the following registry entries
need to be defined:

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FVE`

* `UseAdvancedStartup`: `DWORD:1`
* `EnableBDEWithNoTPM`: `DWORD:1`
* `UseTPM`: `DWORD:2`
* `UseTPMPIN`: `DWORD:2`
* `UseTPMKey`: `DWORD:2`
* `UseTPMKeyPIN`: `DWORD:2`

## Enable better encryption

To use 256-bit AES encryption instead of the 128-bit default, the following
registry entries must be defined:

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FVE`

* `EncryptionMethodNoDiffuser`: `DWORD:4`
* `EncryptionMethod`: `DWORD:2`

## Enable encryption

Enable BitLocker with a password (must be at least 8 characters) and a numeric
recovery password:

```
PS> manage-bde.exe -on C: -Password -RecoveryPassword
```

Using abbreviations:

```
PS> manage-bde.exe -on C: -pw -rp
```

To enable the encryption, the computer needs to be restarted: `shutdown /r /t
0`. If you misspell the password BitLocker will not be enabled.

!!! warning
    The keyboard layout can be different during password prompt of BitLocker.
    Therefore be prepared, if the password is misspelled that you used a
    key that has a different mapping.

## Check status

After first successful restart, the encryption of the volume is starting. The
current progress can be monitored with:

```
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
        Numerical Password
        Password
```

To get the protector methods:

```
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

## Script

The following PowerShell script enable BitLocker on `C:`:

```PowerShell
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
  @{Name = 'UseAdvancedStartup';         Type = 'DWORD'; Value = 1}, `
  @{Name = 'EnableBDEWithNoTPM';         Type = 'DWORD'; Value = 1}, `
  @{Name = 'UseTPM';                     Type = 'DWORD'; Value = 2}, `
  @{Name = 'UseTPMPIN';                  Type = 'DWORD'; Value = 2}, `
  @{Name = 'UseTPMKey';                  Type = 'DWORD'; Value = 2}, `
  @{Name = 'UseTPMKeyPIN';               Type = 'DWORD'; Value = 2}, `
  @{Name = 'EncryptionMethodNoDiffuser'; Type = 'DWORD'; Value = 4}, `
  @{Name = 'EncryptionMethod';           Type = 'DWORD'; Value = 2}

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

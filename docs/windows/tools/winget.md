# winget

Windows Package Manager Client

https://github.com/microsoft/winget-cli

## Install

Download latest version from GitHub
[release page](https://github.com/microsoft/winget-cli/releases).

Install `winget` as a provisioned package:

```powershell
PS> Add-AppxProvisionedPackage
        -PackagePath 'Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.msixbundle'
        -LicensePath '7bcb1a0ab33340daa57fa5b81faec616_License1.xml'
        -DependencyPackagePath
            'Microsoft.VCLibs.x64.14.00.Desktop.appx',
            'Microsoft.UI.Xaml.2.7_7.2208.15002.0_x64__8wekyb3d8bbwe.appx'
        -Online
```

Enable provisioned package for the current user:

```powershell
(Get-AppxProvisionedPackage -Online | 
   Where-Object -Property DisplayName -EQ Microsoft.DesktopAppInstaller
).InstallLocation | Add-AppxPackage -Register -DisableDevelopmentMode
```

The executable `winget.exe` is than found in
`$env:USERPROFILE\AppData\Local\Microsoft\WindowsApps\`.

!!! warning

    If there are policies that does not allow Microsoft applications, the
    application `Microsoft.DesktopAppInstaller` must be whitelisted.

## Searching for Packages

List all available packages:

```powershell
PS> winget search 
```

Can also be seen at
[microsoft/winget-pkgs](https://github.com/microsoft/winget-pkgs/tree/master/manifests).

List all packages that contains `note`:

```powershell
PS> winget search note
```

Display informations about a package:

```powershell
PS> winget show notepad++
```

## Installing a Package

Try to install all packages in machine scope (for all users):

```powershell
PS> winget install --scope machine --exact --id gerardog.gsudo
```

Some package can not be installed in machine scope:

```powershell
PS> winget install --scope user --exact --id Microsoft.WindowsTerminal
```

## Listing Package Repositories

```powershell
PS> winget source list
```

## Upgrading Packages

To get a list of upgradeable packages:

```powershell
PS> winget upgrade
```

To upgrade a package:

```powershell
PS> winget upgrade Notepad++.Notepad++
```

## Configuration Settings

To open the settings in an editor:

```powershell
PS> winget settings
```

The settings ([documentation on these settings](https://aka.ms/winget-settings)):

```json
{
    "$schema": "https://aka.ms/winget-settings.schema.json",

    "visual": {
        "progressBar": "rainbow"
    },
    "installBehavior": {
        "preferences": {
            "scope": "machine"
        }
    },
    "source": {
        "autoUpdateIntervalInMinutes": 5
    }
}
```

* `progressBar`: `accent` or `rainbow`
* `scope`: `user` or `machine`

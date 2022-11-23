# Windows

## Windows Updates

Get stand-alone update packages directly from
[Microsoft](https://www.catalog.update.microsoft.com/).

Get information about current running Windows:

```PS
PS> (Get-ComputerInfo).OSVersion
10.0.19044
```

Explanation of the number can be found
[here](https://learn.microsoft.com/en-us/windows/release-health/release-information).

Get the Window Update Build Revision (UBR):

```PS
PS> (Get-ComputerInfo).UBR
2251
```

Get list of installed updates:

```PS
PS> Get-HotFix | Sort-Object InstalledOn -Descending
```

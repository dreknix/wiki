# Windows Terminal

## Install

https://github.com/microsoft/terminal

Download the latest release from https://github.com/microsoft/terminal/releases/latest

Install the MSIXBundle by starting a PowerShell with administratior rights:

```powershell
PS> Add-AppxPackage Microsoft.WindowsTerminal_1.2.2381.0_8wekyb3d8bbwe.msixbundle
```

Or install via `winget`:

```powershell
PS> winget install "Windows Terminal"
```

## Configure

Global configurations (`copyOnSelect` must be changed):

```json
{
    "disabledProfileSources": [ "Windows.Terminal.Azure" ],
    "theme": "dark",
    "launchMode": "maximized",

    // If enabled, selections are automatically copied to your clipboard.
    "copyOnSelect": true
}
```

Default settings for all profiles:

```json
{
    "profiles":
    {
        "defaults":
        {
            "fontFace": "Hack NF",
            "fontSize" : 14,
            "cursorShape": "filledBox",
            "colorScheme": "Tomorrow Night"
        },
        "list":
        [
        ]
    }
}
```

Add Cygwin bash:

```json
			{
				"guid": "{a1f2ceb2-795d-4f2a-81cc-723cceec49c0}",
				"name": "Cygwin bash",
				"commandline": "C:/cygwin/bin/bash.exe -i -l",
				"icon": "C:/cygwin/Cygwin-Terminal.ico",
				"hidden": false
			},
```

Add `startingDirectory` for all WSL entries:

```json
      {
				"startingDirectory": "//wsl$/Ubuntu-20.04/home/ti5cw"
      }
```

Define the Tomorrow Night color theme:

```json
{
    "schemes": [
        {
            "name": "Tomorrow Night",
            "black": "#000000",
            "red": "#cc6666",
            "green": "#b5bd68",
            "yellow": "#f0c674",
            "blue": "#81a2be",
            "purple": "#b294bb",
            "cyan": "#8abeb7",
            "white": "#ffffff",
            "brightBlack": "#000000",
            "brightRed": "#cc6666",
            "brightGreen": "#b5bd68",
            "brightYellow": "#f0c674",
            "brightBlue": "#81a2be",
            "brightPurple": "#b294bb",
            "brightCyan": "#8abeb7",
            "brightWhite": "#ffffff",
            "background": "#1d1f21",
            "foreground": "#c5c8c6"
        }
    ]
}
```

Change keyboard shortcuts:

```json
{
    "actions":
    [
        // Change binding from Ctrl+= to Ctrl+ß
        { "command": { "action": "adjustFontSize", "delta": 1 }, "keys": "ctrl+ß" }
    ]
}
```

## Shortcuts

* Copy: ++ctrl+shift+c++
* Paste: ++ctrl+shift+v++
* Find: ++ctrl+shift+f++

* Toggle fullscreen: ++alt+return++
* Scroll up: ++ctrl+shift+arrow-up++ / ++ctrl+shift+page-up++
* Scroll down: ++ctrl+shift+arrow-down++ / ++ctrl+shift+page-down++

* Increase Font Size: ++ctrl+period++
* Decrease Font Size: ++ctrl+minus++
* Reset Font Size: ++ctrl+0++

* New Tab: ++ctrl+shift+t++
* New Tab with specific Profile:
  * ++ctrl+shift+1++
  * ++ctrl+shift+2++
  * ...
* Move between Tabs:
  * ++ctrl+tab++
  * ++ctrl+shift+tab++
* Open spefic Tab:
  * ++ctrl+alt+1++
  * ++ctrl+alt+2++
  * ...

* New Pane: ++alt+shift+d++
* Split Pane horizontal: ++alt+shift+minus++
* Split Pane vertical: ++alt+shift+plus++
* Switch between Panes:
  * ++alt+arrow-up++ 
  * ++alt+arrow-down++ 
  * ++alt+arrow-left++ 
  * ++alt+arrow-right++
* Resize current Pane:
  * ++alt+shift+arrow-up++ 
  * ++alt+shift+arrow-down++ 
  * ++alt+shift+arrow-left++ 
  * ++alt+shift+arrow-right++

* Close current Pane: ++ctrl+shift+w++
* Close Window: ++alt+f4++


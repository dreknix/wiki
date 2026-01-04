# Settings in macOS

To find the settings you can use the GUI and compare the output of `defaults
read <domain>` before and after the change.

## General Settings

### Dock

Open Dock settings:
[`open x-apple.systempreferences:com.apple.preferemce.dock`](
x-apple.systempreferences:com.apple.preferemce.dock)

* Auto hide the Dock

  ``` bash
  defaults write com.apple.dock "autohide" -bool true
  ```

* Do not display recent apps in the Dock

  ``` bash
  defaults write com.apple.dock "show-recents" -bool false
  ```

* Disable launch animations

  ``` bash
  defaults write com.apple.dock "launchanim" -bool false
  ```

Finally restart the Dock: `killall Dock`

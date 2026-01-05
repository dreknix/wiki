# Settings in macOS

Open macOS settings:
[`open x-apple.systempreferences:com.apple.preference.general`](
x-apple.systempreferences:com.apple.preference.general)

To find the settings you can use the GUI and compare the output of `defaults
read <domain>` before and after the change.

## General Settings

### Dock

Open Dock settings:
[`open x-apple.systempreferences:com.apple.preference.dock`](
x-apple.systempreferences:com.apple.preference.dock)

* Auto hide the Dock

  ``` bash
  defaults write com.apple.dock "autohide" -bool true
  ```

* Do not display recent apps in the Dock

  ``` bash
  defaults write com.apple.dock "show-recents" -bool false
  ```

* Disable launch animations and minimize effect

  ``` bash
  defaults write com.apple.dock "launchanim" -bool false
  defaults write com.apple.dock "mineffect" "scale"
  ```

* Set Dock size and disable magnification

  ``` bash
  defaults write com.apple.dock "magnification" -bool false
  defaults write com.apple.dock "tilesize" -int 40
  ```

Finally restart the Dock: `killall Dock`

### Desktop & Stage Manager

Open desktop settings (part of Dock settings):
[`open x-apple.systempreferences:com.apple.preference.dock`](
x-apple.systempreferences:com.apple.preference.dock)

* Disable reveal desktop on click

  ``` bash
  defaults write com.apple.WindowManager "EnableStandardClickToShowDesktop" -bool false
  ```

* Hide files on desktop and stage manager

  ``` bash
  defaults write com.apple.WindowManager "HideDesktop" -bool true
  defaults write com.apple.WindowManager "StandardHideDesktopIcons" -bool true
  ```

* Do not show recent apps in stage manager

  ``` bash
  defaults write com.apple.WindowManager "AutoHide" -bool true
  ```

* Disable widgets on desktop and stage manager

  ``` bash
  defaults write com.apple.WindowManager "StandardHideWidgets" -bool true
  defaults write com.apple.WindowManager "StageManagerHideWidgets" -bool true
  ```

## macOS Applications

### Finder

* Set home directory a default start directory

  ``` bash
  defaults write com.apple.finder "NewWindowTarget" "PfHm"
  ```

* Open new folder in new tab

  ``` bash
  defaults write com.apple.finder "FinderSpawnTab" -bool true
  ```

* Do not show drives and servers

  ``` bash
  defaults write com.apple.finder "ShowExternalHardDrivesOnDesktop" -bool false
  defaults write com.apple.finder "ShowHardDrivesOnDesktop" -bool false
  defaults write com.apple.finder "ShowMountedServersOnDesktop" -bool false
  defaults write com.apple.finder "ShowRemovableMediaOnDesktop" -bool false
  ```

* Do not show a warning when changing file extensions

  ``` bash
  defaults write com.apple.finder "FXEnableExtensionChangeWarning" -bool false
  ```

* Set default search scope to current folder

  ``` bash
  defaults write com.apple.finder "FXDefaultSearchScope" "SCcf"
  ```

* Remove old files (older than 30 days) from trash

  ``` bash
  defaults write com.apple.finder "FXRemoveOldTrashItems" -bool true
  ```

* Show path and status bar

  ``` bash
  defaults write com.apple.finder "ShowPathbar" -bool true
  defaults write com.apple.finder "ShowStatusBar" -bool true
  ```

### Screenshots

* Change location for screenshots

  ``` bash
  defaults write com.apple.screencapture "location" "~/Downloads/Screenshots"
  ```

  You must also create the directory.

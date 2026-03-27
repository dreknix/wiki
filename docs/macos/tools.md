# Tools for macOS

These tools are specific for macOS.

## AltTab

## Hidden Bar

## Karabiner

## Raycast

## Rectangle

## dmenu

The version of [`dmenu`](https://tools.suckless.org/dmenu/) in `HomeBrew` is not
working. The call to open `XOpenIM` is failing. Therefore, the tool must the
compiled to support [`XQuartz`](https://www.xquartz.org/).

``` console
brew install --cask xquartz
```

Download the sources of `dmenu` and change the following three lines in the
file `config.mk`:

``` plain
X11INC = /opt/X11/include
X11LIB = /opt/X11/lib

...

FREETYPEINC = /opt/X11/include/freetype2
```

Now `dmenu` is using the libraries of `XQuartz` and not those of `Homebrew`.

In order to use `dmenu` the focus must first be changed to the newly opened
window and afterwards back to the previous application.

This can be achieved with the following snippet:

``` console
open -a XQuartz; ls | dmenu -i; \
  osascript -e 'tell application "System Events" to keystroke tab using {command down}'
```

## Automator for running shell script via shortcuts

* Open the application `Automator`

* Choose type from list: `Application`

* Select `Run Shell Script` from the list and drag into the right space

* Write the shell script, e.g.,

  ``` console
  source ~/.zprofile
  ~/bin/gnome-key-gopass.sh
  ```

  Sourcing the file `~/.zprofile` give access to all tools installed via
  `HomeBrew`.

  To allow `oascript` send a keystroke within the script, the application
  `Automator` needs additional privileges. Open `Settings` > `Privacy &
  Security` > `Accessibility`. Allow the application `Automator`.

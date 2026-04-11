# Homebrew

## Setup

TODO

Homebrew installs standard tools always with the GNU prefix `g`. In order to use
scripts that runs also on standard Linux systems, the binaries in the `gnubin`
directory must be included into `${PATH}` and `${MANPATH}` environment
variables.

``` bash
# add homebrew installed tools to PATH and MANPATH
# coreutils
PATH="$(brew --prefix coreutils)/libexec/gnubin:${PATH}"
MANPATH="$(brew --prefix coreutils)/libexec/gnuman:${MANPATH}"
# sed
PATH="$(brew --prefix gnu-sed)/libexec/gnubin:${PATH}"
MANPATH="$(brew --prefix gnu-sed)/libexec/gnuman:${MANPATH}"
# xargs
PATH="$(brew --prefix findutils)/libexec/gnubin:${PATH}"
MANPATH="$(brew --prefix findutils)/libexec/gnuman:${MANPATH}"
export PATH
export MANPATH
```

## Install older version

``` console
brew tap-new local/neovim
brew tap homebrew/core --force
brew extract --version 0.11.7 neovim local/neovim
brew install local/neovim/neovim@0.11.7
```

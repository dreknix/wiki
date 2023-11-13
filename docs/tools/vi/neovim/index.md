# Neovim / NvChad

[:fontawesome-brands-github: Neovim](https://github.com/neovim/neovim/){:target="_blank"}
is a fork of Vim with builtin support for Language Server Protocol (LSP)
and Lua scripting.

For Neovim different advanced configurations exists:
[LazyVim](https://github.com/LazyVim/LazyVim/){:target="_blank"},
[LunarVim](https://github.com/LunarVim/LunarVim/){:target="_blank"},
[AstroNvim](https://github.com/AstroNvim/AstroNvim/){:target="_blank"},
[NvChad](https://github.com/NvChad/NvChad/){:target="_blank"}, ...
In the following NvChad is used as the bases for the custom configuration.

* [:fontawesome-brands-github: NvChad/NvChad](https://github.com/NvChad/NvChad/){:target="_blank"}
* [:fontawesome-brands-github: NvChad/example_config](https://github.com/NvChad/example_config/){:target="_blank"}

## Install/Update

Install (Linux):

```console
$ rm -rf ~/.config/nvim
$ rm -rf ~/.local/share/nvim
$ git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
```

Install (Windows):

```ps1
PS> rd -r ~\AppData\Local\nvim
PS> rd -r ~\AppData\Local\nvim-data
PS> git clone https://github.com/NvChad/NvChad $HOME\AppData\Local\nvim --depth 1
```

Update:

```vim
:NvChadUpdate
```

## Key Mappings and Commands

!!! note
    In NvChad the leader key is ++space++.

* change theme: `<leader> + th`
* cheat sheet: `<leader> + ch` / `:NvCheatsheet`
* dash screen: `:Nvdash`
* file tree: `:NvimTreeOpen` / `<leader> + e`
* searchable keymaps: `:Telescope keymaps`
* list of LSP packages: `:Mason`
* install all configured LSP packages: `:MasonInstallAll`
* find files: `<leader> + f f`
* recent files: `<leader> + f o`
* find words (live grep): `<leader> + f w`
* find bookmarks: `<leader> + m a`
* floating terminal: ++alt++ + `i`
* horizontal terminal: ++alt++ + `h` / `<leader> + h`
* vertical terminal: ++alt++ + `v` / `<leader> + v`
* next buffer: ++tab++
* previous buffer: ++shift++ + ++tab++
* new buffer: `<leader> + b`
* close buffer: `<leader> + x`

## Custom Config

[:fontawesome-brands-github: dreknix/tools-nvchad-config](https://github.com/dreknix/tools-nvchad-config/){:target="_blank"}

```
~/.config/nvim/lua/custom/
├── init.lua
├── chadrc.lua
├── mappings.lua
├── plugins.lua
├── configs/
│   ├── lspconfig.lua
│   └── overrides.lua
├── after/
└── snippets/
```

### Config Files

Custom configuration files:

* [`init.lua`](init_lua.md)
* [`chadrc.lua`](chadrc_lua.md)
* [`mappings.lua`](mappings_lua.md)
* [`plugins.lua`](plugins_lua.md)
* [`configs/lspconfig.lua`](configs_lspconfig_lua.md)
* [`configs/overrides.lua`](configs_overrides_lua.md)

### After Directory

Specific settings for each file type can be found in
`after/ftplugin/<filetype>.lua`.

### Snippets

NvChad uses the following plugins for snippets:

* [:fontawesome-brands-github: L3MON4D3/LuaSnip](https://github.com/L3MON4D3/LuaSnip/){:target="_blank"}
* [:fontawesome-brands-github: rafamadriz/friendly-snippets](https://github.com/rafamadriz/friendly-snippets/){:target="_blank"}

The predefined snippets are installed in `~/.local/share/nvim/lazy/friendly-snippets/snippets/`. Custom snippets are loaded in [`init.lua`](init_lua.md).

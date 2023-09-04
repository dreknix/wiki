# Neovim / NvChad

* [:fontawesome-brands-github: neovim/neovim](https://github.com/neovim/neovim/)
* [:fontawesome-brands-github: NvChad/NvChad](https://github.com/NvChad/NvChad/)
* [:fontawesome-brands-github: NvChad/example_config](https://github.com/NvChad/example_config/)

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

## Commands

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

## Custom Config

```
~/.config/nvim/lua/custom/
├── init.lua
├── chadrc.lua
├── configs/
│   ├── lspconfig.lua
│   └── overrides.lua
├── snippets/
├── mappings.lua
└── plugins.lua
```

Custom configuration files:

* [`init.lua`](init_lua.md)
* [`chadrc.lua`](chadrc_lua.md)
* [`configs/lspconfig.lua`](configs_lspconfig_lua.md)
* [`configs/overrides.lua`](configs_overrides_lua.md)
* [`mappings.lua`](mappings_lua.md)
* [`plugins.lua`](plugins_lua.md)

## Snippets

NvChad uses the following plugins for snippets:

* [:fontawesome-brands-github: L3MON4D3/LuaSnip](https://github.com/L3MON4D3/LuaSnip/)
* [:fontawesome-brands-github: rafamadriz/friendly-snippets](https://github.com/rafamadriz/friendly-snippets/)

The predefined snippets are installed in `~/.local/share/nvim/lazy/friendly-snippets/snippets/`. Custom snippets are loaded in [`init.lua`](init_lua.md).

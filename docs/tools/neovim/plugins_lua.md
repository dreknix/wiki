# plugins.md

[Overrides](configs_lspconfig_lua.md) already loaded NvChad plugins:

* [:fontawesome-brands-github: williamboman/mason.nvim](https://github.com/williamboman/mason.nvim/)
* [:fontawesome-brands-github: nvim-treesitter/nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter/)
* [:fontawesome-brands-github: nvim-tree/nvim-tree.lua](https://github.com/nvim-tree/nvim-tree.lua/)

Add LSP servers in [`configs/lspconfig.lua`](configs_lspconfig_lua.md).

Add additional plugins:

* [:fontawesome-brands-github: christoomey/vim-tmux-navigator](https://github.com/christoomey/vim-tmux-navigator/)

Content of `~/.config/nvim/lua/custom/plugins.lua`:

```lua
local overrides = require 'custom.configs.overrides'

local plugins = {
  {
    "williamboman/mason.nvim",
    opts = overrides.mason,
  },
  {
    'nvim-treesitter/nvim-treesitter',
    opts = overrides.treesitter,
  },
  {
    'nvim-tree/nvim-tree.lua',
    opts = overrides.nvimtree,
  },
  {
    'neovim/nvim-lspconfig',
    config = function()
      require 'plugins.configs.lspconfig'
      require 'custom.configs.lspconfig'
    end
  },
  {
    'christoomey/vim-tmux-navigator',
    lazy = false,
  }
}

return plugins
```

# plugins.md

[Overrides](configs_lspconfig_lua.md) already loaded NvChad plugins:

* [:fontawesome-brands-github: williamboman/mason.nvim](https://github.com/williamboman/mason.nvim/){:target="_blank"}
* [:fontawesome-brands-github: nvim-treesitter/nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter/){:target="_blank"}
* [:fontawesome-brands-github: nvim-tree/nvim-tree.lua](https://github.com/nvim-tree/nvim-tree.lua/){:target="_blank"}
* [:fontawesome-brands-github: lukas-reineke/indent-blankline.nvim](https://github.com/lukas-reineke/indent-blankline.nvim/){:target="_blank"}

Add LSP servers in [`configs/lspconfig.lua`](configs_lspconfig_lua.md).

Add additional plugins:

* [:fontawesome-brands-github: christoomey/vim-tmux-navigator](https://github.com/christoomey/vim-tmux-navigator/){:target="_blank"}

Content of `~/.config/nvim/lua/custom/plugins.lua`:

```lua
local overrides = require 'custom.configs.overrides'

local plugins = {
  {
    'williamboman/mason.nvim',
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
    'lukas-reineke/indent-blankline.nvim',
    opts = overrides.blankline,
  },
  {
    'neovim/nvim-lspconfig',
    config = function()
      require 'plugins.configs.lspconfig'
      require 'custom.configs.lspconfig'
    end
  },
  {
    'mfussenegger/nvim-lint',
    event = 'VeryLazy',
    config = function()
      require 'custom.configs.lint'
    end
  },
  {
    'mhartington/formatter.nvim',
    event = 'VeryLazy',
    opts = function()
      return require 'custom.configs.formatter'
    end
  },
  {
    'mfussenegger/nvim-dap',
    config = function(_, opts)
      require('core.utils').load_mappings('dap')
    end
  },
  {
    'rcarriga/nvim-dap-ui',
    dependencies = 'mfussenegger/nvim-dap',
    config = function()
      local dap = require('dap')
      local dapui = require('dapui')
      dapui.setup()
      dap.listeners.after.event_initialized['dapui_config'] = function()
        dapui.open()
      end
      dap.listeners.before.event_terminated['dapui_config'] = function()
        dapui.close()
      end
      dap.listeners.before.event_exited['dapui_config'] = function()
        dapui.close()
      end
    end
  },
  {
    'mfussenegger/nvim-dap-python',
    ft = 'python',
    dependencies = {
      'mfussenegger/nvim-dap',
      'rcarriga/nvim-dap-ui',
    },
    config = function(_, opts)
      local path = '~/.local/share/nvim/mason/packages/debugpy/venv/bin/python'
      require('dap-python').setup(path)
      require('core.utils').load_mappings('dap_python')
    end,
  },
  {
    'christoomey/vim-tmux-navigator',
    lazy = false,
  }
}

return plugins
```

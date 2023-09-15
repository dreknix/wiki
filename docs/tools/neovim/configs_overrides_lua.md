# configs/overrides.lua

Content of `~/.config/nvim/lua/custom/configs/overrides.lua`:

```lua
--
-- configure: williamboman/mason.nvim
--

M.mason = {
  ensure_installed = {
    -- lua stuff
    'lua-language-server',
    'stylua',

    -- web dev stuff
    'css-lsp',
    'html-lsp',
    'prettier',

    -- c/cpp stuff
    'clangd',
    'clang-format',

    -- shell stuff
    'ansible-language-server',
    'bash-language-server',
    'docker-compose-language-service',
    'dockerfile-language-server',
    'jsonlint',
    'marksman',
    'yaml-language-server',

    -- python stuff
    'pyright',
    'flake8',
    'black',
    'pylint',
    'mypy',
    'ruff',
    'debugpy',
  },
}
```

```lua
--
-- configure: nvim-treesitter/nvim-treesitter
--
M.treesitter = {
  ensure_installed = {
    'bash',
    'c',
    'cpp',
    'css',
    'git_config',
    'git_rebase',
    'gitcommit',
    'gitignore',
    'go',
    'html',
    'ini',
    'java',
    'javascript',
    'jq',
    'json',
    'latex',
    'lua',
    'make',
    'markdown',
    'markdown_inline',
    'mermaid',
    'perl',
    'php',
    'python',
    'sql',
    'xml',
    'yaml',
  },
  indent = {
    enable = true,
    -- disable = {
    --   'python'
    -- },
  },
}
```

```lua
--
-- configure: nvim-tree/nvim-tree.lua
--

M.nvimtree = {
  git = {
    enable = true,
  },

  renderer = {
    highlight_git = true,
    icons = {
      show = {
        git = true,
      },
    },
  },
}
```

#

## lukas-reineke/indent-blankline.nvim

Define other symbols for visualizing different whitespaces in list mode. The
list mode is disabled by default. ++f2++ is used to toogle list view (see
[mappings.lua](mappings_lua.md)). With the plugin variable `show_end_of_line`
the character set defined by `listchars` is used.

```lua
vim.opt.list = false
vim.opt.listchars:append({ tab   = '→ ' })  -- u2192
vim.opt.listchars:append({ eol   = '↲'  })  -- u21b2
vim.opt.listchars:append({ space = '·'  })  -- u00b7
vim.opt.listchars:append({ nbsp  = '␣'  })  -- u2423
vim.opt.listchars:append({ trail = '☐'  })  -- u2610

M.blankline = {
  show_end_of_line = true,
}
```

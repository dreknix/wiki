# configs/overrides.lua

Content of `~/.config/nvim/lua/custom/configs/overrides.lua`:

```lua
local M = {}

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
  },
}

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

return M
```

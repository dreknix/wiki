# init.lua

General initialization of Neovim that is not handled by a plugin or NvChad.

## General settings

Highlight the columns `textwidth+0` and `textwidth+20`. The default value of
`textwidth` is `80`.

```lua
vim.opt.colorcolumn = '+0,+20'
```

:construction: **TODO** move to overrides or configs

```lua
-- LuaSnip uses rafamadriz/friendly-snippets
-- extend in snipmate format
vim.g.snipmate_snippets_path = vim.fn.stdpath "config" .. "/lua/custom/snippets"
```

## File Types

Add file types to specific file name patterns (
see [vim.filetype](https://neovim.io/doc/user/lua.html#vim.filetype){:target="_blank"}). The file
type `yaml.docker-compose` and `yaml.ansible` is used for choosing a more
specific LSP server.

```lua
vim.filetype.add({
  pattern = {
    -- SSH config
    ['.*/.ssh/config.d/.*'] = 'sshconfig',
    -- rofi config
    ['.*/.*.rasi'] = 'css',
    -- Docker compose
    ['compose.ya?ml'] = 'yaml.docker-compose',
    ['docker-compose.ya?ml'] = 'yaml.docker-compose',
    -- Ansible
    ['.*/playbooks/.*.ya?ml'] = 'yaml.ansible',
    ['.*/playbooks/.*/.*.ya?ml'] = 'yaml.ansible',
    ['.*/roles/.*/tasks/.*.ya?ml'] = 'yaml.ansible',
    ['.*/roles/.*/handlers/.*.ya?ml'] = 'yaml.ansible',
  },
})
```

## Securing gopass

The tool gopass might use Neovim for editing secrets. Therefore, backup files or
other external information storage must be disabled.

```lua
vim.api.nvim_create_autocmd({"BufNewFile", "BufRead"}, {
  pattern = {'/dev/shm/gopass*'},
  callback = function(ev)
    vim.opt_local.swapfile = false
    vim.opt_local.backup = false
    vim.opt_local.undofile = false
    vim.opt_local.shadafile = "NONE"
  end
})
```

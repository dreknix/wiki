# init.lua

Content of `~/.config/nvim/lua/custom/init.lua`:

```lua
vim.opt.colorcolumn = "80"

-- LuaSnip uses rafamadriz/friendly-snippets
-- extend in snipmate format
vim.g.snipmate_snippets_path = vim.fn.stdpath "config" .. "/lua/custom/snippets"

--
-- https://neovim.io/doc/user/lua.html#vim.filetype
--
vim.filetype.add({
  pattern = {
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

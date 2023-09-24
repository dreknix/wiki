# configs/lspconfig.lua

Configuration of the LSP servers can be found [here](https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md){:target="_blank"}.

The LSP configuration for the current buffer can be shown with `:LspInfo`. Often the file type (`:set ft`) must be set to the correct value. See `vim.filetype.add` in [init.lua](init_lua.md).

Content of `~/.config/nvim/lua/custom/configs/lspconfig.lua`:

```lua
local base = require('plugins.configs.lspconfig')

local on_attach = base.on_attach
local capabilities = base.capabilities

local lspconfig = require('lspconfig')

lspconfig.clangd.setup({
  on_attach = function (client, bufnr)
    client.server_capabilities.signatureHelpProvider = false
    on_attach(client, bufnr)
  end,
  capabilities = capabilities,
})

lspconfig.ansiblels.setup({
  on_attach = on_attach,
  capabilities = capabilities,
})

lspconfig.docker_compose_language_service.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {'yaml.docker-compose'},
})

lspconfig.dockerls.setup({
  on_attach = on_attach,
  capabilities = capabilities,
})

lspconfig.jsonls.setup({
  on_attach = on_attach,
  capabilities = capabilities,
})

lspconfig.marksman.setup({
  on_attach = on_attach,
  capabilities = capabilities,
})

lspconfig.yamlls.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {'yaml', 'yaml.ansible', 'yaml.docker-compose'},
})

lspconfig.pyright.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {'python'},
})
```

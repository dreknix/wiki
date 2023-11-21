---
date: 2023-11-21
categories:
  - Neovim
---

# Enable `gx` in NvChad

The command `gx` opens the filename under the the cursor with `vim.ui.open()`
(see [`:help netrw-gx`](https://neovim.io/doc/user/pi_netrw.html#netrw-gx){target="_blank"}).
Then handling is provided by the internal `netrw` plugin. This plugin is
disabled when using [NvChad](../../tools/vi/neovim/index.md).

<!-- more -->

NvChad is using [:fontawesome-brands-github: nvim-tree/nvim-tree.lua](
https://github.com/nvim-tree/nvim-tree.lua){target="_blank"}. In the
configuration of this plugin, NvChad disables `netrw`, but allows the usage
of `netrw` by the hijacking setup option (see [`lua/plugins/configs/nvimtree.lua`](
https://github.com/NvChad/NvChad/blob/v2.0/lua/plugins/configs/nvimtree.lua){target="_blank"}).

This options will allow the usage of `gx`. But NvChad also disables the plugin
in the configuration of the plugin manager [:fontawesome-brands-github: folke/lazy.nvim](
https://github.com/folke/lazy.nvim){target="_blank"}.
The `netrw` plugin is disabled with the option
`performance.rtp.disabled_plugins` in the file [`plugins/configs/lazy_nvim.lua`](
https://github.com/NvChad/NvChad/blob/v2.0/lua/plugins/configs/lazy_nvim.lua){target="_blank"}.

To enable the `gx` command the entry `netrwPlugin` must be removed from this
option. But the configuration mechanism in NvChad allows only to overwrite an
option. Therefore the most straightforward way is to overwrite the option with
the complete list without `netrwPlugin` in `custom/chadrc.lua`:

``` lua title="chadrc.lua"
local M = {}
M.lazy_vim = {
  performance = {
    rtp = {
      disabled_plugins = {
        "2html_plugin",
        ...
        "ftplugin",
      }
    }
  }
}
return M
```

The drawback of this method is, that whenever the NvChad configuration is
changed, this list must be updated.

The better solution is to load the list and remove the favored plugins:

``` lua title="chadrc.lua"
local M = {}
-- load default list of disabled plugins in NvChad
local default = require('plugins.configs.lazy_nvim')
local default_disabled_plugins = default.performance.rtp.disabled_plugins

-- specify which plugins should be removed from default disabled list
local enabled = {
  netrwPlugin = true,
}
-- enable those plugins
local i=1
while i <= #default_disabled_plugins do
    if enabled[default_disabled_plugins[i]] then
        table.remove(default_disabled_plugins, i)
    else
        i = i + 1
    end
end

M.lazy_nvim = {
  performance = {
    rtp = {
      disabled_plugins = default_disabled_plugins,
    },
  },
}
return M
```

!!! info
    When you only want to use the `gx` command, there are other solutions like
    using a separate plugin, e.g., [:fontawesome-brands-github: chrishrb/gx.nvim](
    https://github.com/chrishrb/gx.nvim){target="_blank"}. This is also an
    example, how to overwrite configuration settings by removing an option in
    NvChad.

!!! tip
    A table can be printed with the following commands:

    ``` lua
    print(vim.inspect(require('plugins.configs.lazy_nvim')))
    print(vim.inspect(default_disabled_plugins))
    ```

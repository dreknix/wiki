# chardrc.lua

Content of `~/.config/nvim/lua/custom/chadrc.lua`:

```lua
---@type ChadrcConfig
 local M = {}
 M.ui = {theme = 'catppuccin'}
 M.plugins = 'custom.plugins'
 M.mappings = require 'custom.mappings'
 return M
```

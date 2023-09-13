# mappings.lua

Content of `~/.config/nvim/lua/custom/mappings.lua`:

```lua
local M = {}

M.general = {
  n = {
    -- switch between windows
    ["<A-Left>"] = { "<cmd> TmuxNavigateLeft<CR>", "Window left" },
    ["<A-Right>"] = { "<cmd> TmuxNavigateRight<CR>", "Window right" },
    ["<A-Down>"] = { "<cmd> TmuxNavigateDown<CR>", "Window down" },
    ["<A-Up>"] = { "<cmd> TmuxNavigateUp<CR>", "Window up" },
  }
}

return M
```

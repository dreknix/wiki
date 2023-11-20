---
date: 2023-11-20
categories:
  - LaTeX
---

# Using TeX files in Vi

In Vim/Neovim the filetype plugin uses three different type for different TeX
flavors (see [`:help ft-tex-plugin`](
https://neovim.io/doc/user/filetype.html#ft-tex-plugin){target="_blank"}).

<!-- more -->

* TeX: `plaintex`
* LaTeX: `tex`
* ConTeXt: `context`

Which filetype the current buffer was assigned, can be shown with `:set
filetype?` (or `:LspInfo` if LSP is enabled).

All files with the extension `*.tex` are of type `plaintex`. When the file type
plugin finds specific keywords the file type is than changed to `tex` or
`context`.

The automatic guessing can be disabled with the following two mechanisms.

Add the TeX flavor in the first line of a `*.tex` file:

``` tex
%&latex
```

Or set the global variable `tex_flavor`:

=== "Vim"

    ``` vim
    let g:tex_flavour = "latex"
    ```

=== "Neovim"

    ``` lua
    vim.g.tex_flavor = 'latex'
    ```

    See [`:help lua-guide-variables`](
    https://neovim.io/doc/user/lua-guide.html#lua-guide-variables){target="_blank"}
    for more information about the Lua wrappers for different variable scopes.

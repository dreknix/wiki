# vim

https://github.com/mhinz/vim-galore

## General

```vim
" enter current millenium
set nocompatible

" enable syntax and plugins (for netrw)
syntax enable
filetype plugin on
```

## Things to learn

```vim
" cip cap dip dap
" ciw
" ci"
" ci'
" ci(
" ci{
" ci<
" cit
```

## Finding Files

```vim
" search down into subfolders
" provides tab-completion for all file-related tasks
set path+=**

" ignore different file types
set wildignore+=*.aux,*.log,*.out,*.pdf,*.o

" ignore a specific folder
set wildignore+=**/node_modules/**

" give suffixes a low priority (list at end)
set suffixes+=.pdf

" display all matching files when we tab complete
set wildmenu

" Hit tab to :find by partial match
" Use * to make it fuzzy

" :b lets you autocomplete any open buffer
```

## Autocomplete

More shortcuts can be found in |ins-completion|

```vim
" ^x^n - for just this file
" ^x^f - for filenames
" ^x^] - for tags only
" ^n   - for anything by the 'complete' option

" move than with ^n and ^p in the suggestion list
```

## Spell Checking and Word Completion

```vim
" Spell-check language
set spelllang=en_us

" Automatically set by plugin vim-markdown
"" Define new filetype markdown
"autocmd BufRead,BufNewFile *.md set filetype=markdown

" Spell-check Markdown files
autocmd FileType markdown setlocal spell

" Spell-check Git messages
autocmd FileType gitcommit setlocal spell

" Set spellfile to location that is guaranteed to exist,
" can be symlinked to Dropbox or kept in Git
" and managed outside of thoughtbot/dotfiles using rcm.
set spellfile=$HOME/.vim/spell-en.utf-8.add

" Autocomplete with dictionary words when spell check is on
set complete+=kspell
```

## File Browsing

```vim
" tweaks for browsing
let g:netrw_banner=0        " disable annoying banner
let g:netrw_browse_split=4  " open in prior window
let g:netrw_altv=1          " open splits to the right
let g:netrw_liststyle=3     " tree view
let g:netrw_list_hide=netrw_gitignore#Hide()
let g:netrw_list_hide.=',\(^\|\s\s\)\zs\.\S\+'

" :edit a folder to open a file browser
" <CR>/v/t to open in an h-split/v-split/tab
" check |netrw-browse-maps| for more mappings
```

## Snippets

```vim
" read an empty HTML template and move cursor to title
nnoremap ,html :-1read $HOME/.vim/.skeleton.html<CR>3jwf>a
```

## Help

```vim
:help ^n    " help about ctrl-n in normal mode
:help i_^n  " help about ctrl-n in insert mode
:help c_^n  " help about ctrl-n in command mode

" grep in help pages
:helpgrep xxx
```

## ColorColumn

```vim
set textwidth=80
set colorcolumn=81
highlight ColorColumn ctermbg=0 guibg=lightgrey

" alternativ
au BufWinEnter * let w:m2=matchadd('ErrorMsg', '\%>80v.\+', -1)
```

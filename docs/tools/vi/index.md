# Vi / Vim / Neovim

## General Informations

There are several starting point for informations:

* Quick reference guide: [`:help quickref`](https://vimhelp.org/quickref.txt.html){:target="_blank"}
* User manual: [`:help usr_toc`](https://vimhelp.org/usr_toc.txt.html){:target="_blank"}
* Reference manual: [`:help reference_toc`](https://vimhelp.org/#reference_toc){:target="_blank"}

## Motions

See: [`:help motion`](https://vimhelp.org/motion.txt.html){:target="_blank"}

### Left-Right Motions

See: [`:help left-right-motions`](https://vimhelp.org/motion.txt.html#left-right-motions){:target="_blank"}

* `0` - move to first character in current line
* `^` - move to first non-blank character in current line
* `$` - move to last character in current line
* `f`*`char`* - move to character *`char`* to the right
  * `cf>` - <b>c</b>hange up to next character ``>`` - including match
* `F`*`char`* - move to character *`char`* to the left
* `t`*`char`* - move to before character *`char`* to the right
  * `dt)` - <b>d</b>elete up <b>t</b>o next character ``)`` - excluding match
* `T`*`char`* - move to after character *`char`* to the left
* `;` - repeat latest `f`, `F`, `t`, `T` movement
* `,` - repeat latest `f`, `F`, `t`, `T` movement in opposite direction

### Up-Down Motions

See: [`:help up-down-motions`](https://vimhelp.org/motion.txt.html#up-down-motions){:target="_blank"}

* `gg` - go to first line in file
* `G` - go to last line in file
* *`count`*`G` - go to last *`count`* line in file
* *`count`*`go` - go to *`count`* byte

### Word Motions

See: [`:help word-motions`](https://vimhelp.org/motion.txt.html#word-motions){:target="_blank"}

* `w` - word forward
* `W` - WORDS forward (WORDS are words with special characters)

### Text Object Motions

See: [`:help object-motions`](https://vimhelp.org/motion.txt.html#word-object){:target="_blank"}

* `)` - sentence forward
* `}` - paragraph forward
* `]]` - section forward

### Text Object Selection

See: [`:help text-objects`](https://vimhelp.org/motion.txt.html#text-objects){:target="_blank"}

* commands starting with `a` selects <b>a</b>n object with whitespaces
* commands starting with `i` selects an <b>i</b>nner object without whitespaces
* objects
  * quotes: `da"` - <b>d</b>elete <b>a</b>round quotes or `da'`
  * brackets: `di]` - <b>d</b>elete <b>i</b>nside brackets
  * braces: `ci{` - <b>c</b>hange <b>i</b>nside braces
  * paragraph: `dap` - <b>d</b>elete <b>i</b>nside paragraph
  * word: `vaw` - <b>v</b>isual select <b>a</b>round word
  * WORD: `vaW` - <b>v</b>isual select <b>a</b>round WORD
  * tag: `vit` - <b>v</b>isual select <b>i</b>nside tag or `vi<`

## Quickfix

See: [`:help quickfix`](https://vimhelp.org/quickfix.txt.html){:target="_blank"}

The quickfix commands are used to work with a list of positions in files. A
location list is a window-local quickfix list.

`:vim word **` or `:vim /pattern/ **` searches the word or pattern in all files
recursively beneath the current directory. And opens the first location. After
that the following commands are working on this quickfix list.

* `:copen` - open the quickfix list
* `:cfirst` / `:clast` - open the first resp. last location
* `:cnext` / `:cprev` - open the next resp. previous location
* `:cc4` - open the fourth location
* `:cclose` or `:ccl` - close the quickfix list
* `:colder` / `:cnewer` - open the older resp. newer quickfix list (within the
last ten)
* `:cdo` *cmd* - execute *cmd* on each location
  * `:cdo s/Foo/Bar/` - replace Foo with Bar at each location
  * `:cdo s/Foo/Bar/ | update` - store each buffer after executing the command
* `:cfdo` *cmd* - execute *cmd* on each file in the quickfix list
  * `:cdo bd` - close all buffers from the quickfix list

## Commands starting with `g`

See: [`:help *g*`](https://vimhelp.org/index.txt.html#g){:target="_blank"}

* `ga` - print ASCII value of character under the cursor
* `gq`*`motion`* - format text described by *`motion`*
* `gw`*`motion`* - format text described by *`motion`* without cursor movement

## Misc

### Clear Search Highlight

With the option `:hlsearch` all matches of the last search are highlighted. To
clear all highlights the command `:nohlsearch` can be used. Often this command
is mapped to `C-L`. In NeoVim the following default mapping exists:

```
nnoremap <C-L> <Cmd>nohlsearch<Bar>diffupdate<Bar>normal! <C-L><CR>
```

### Indenting Code

With `==` or `=` code can be formatted.

* `==` - indent current line
  * `>>` - increase current indentation level be one
  * `<<` - decrease current indentation level be one
* `4==` - indent four lines (including current line)
  * `V3j=` - same indentation by using visual selection
* `=ap` - indent current paragraph
* `gg=G` - indent whole file
* `=%` - when cursor is on `{` or `}` indent whole code block

### Remove Lines Matching a Pattern

* `:g/pattern/d` - remove lines that ^^**do**^^ match the pattern
* `:g!/pattern/d` - remove lines that ^^**do not**^^ match the pattern

### Non-Greedy Regular Expression

See: [`:help non-greedy`](https://vimhelp.org/pattern.txt.html#non-greedy){:target="_blank"}

```json
{
  "key1": "valueA",
  "key2": "valueB"
}
```

The regular expression `".*"` will select `"key1": "valueA"` because it is
greedy. It tries to match as many as possible characters.

The regular expression `".\{-\}"` will select only `"key1"`. It will match the
fewest number of characters as possible. The last `\` is optional, so the
expression `".\{-}"` is also possible.

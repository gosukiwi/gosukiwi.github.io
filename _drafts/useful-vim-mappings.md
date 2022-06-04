---
layout: post
title:  "Useful Vim Mappings"
date:   2022-04-29
categories: vim webdev
---
These are some of the mappings I use in my daily _vimming_ which have passed
the test of time.

In this post I'd like to share them, hopefully you will find them as useful as
I do :)

## Moving lines up and down
Use `<C-j>` and `<C-k>` to move lines up and down in normal, insert and visual
mode.

<script id="asciicast-gkaVClcF6vLq7llseOpZ7BYI6" src="https://asciinema.org/a/gkaVClcF6vLq7llseOpZ7BYI6.js" async></script>

```vimscript
" taken from https://vim.fandom.com/wiki/Moving_lines_up_or_down
nnoremap <C-j> :move .+1<CR>==
nnoremap <C-k> :move .-2<CR>==
inoremap <C-j> <ESC>:move .+1<CR>==gi
inoremap <C-k> <ESC>:move .-2<CR>==gi
vnoremap <C-j> :move '>+1<CR>gv=gv
vnoremap <C-k> :move '<-2<CR>gv=gv
```

## Indent lines
In visual mode, indent/dedent selection with `<TAB>`/`<S-TAB>`:

<script id="asciicast-a1U73Uh7crcDDj6nApKO8rsSI" src="https://asciinema.org/a/a1U73Uh7crcDDj6nApKO8rsSI.js" async></script>

```vimscript
vnoremap <Tab> >gv
vnoremap <S-Tab> <gv
```

## Center screen when finding next/previous matches

```vimscript
nnoremap n nzz
nnoremap N Nzz
```

## Substitute in line

```vimscript
vnoremap gs "zy:s/<C-r>z//g<Left><Left>
nnoremap gs "zyiw:s/<C-r>z//g<Left><Left>
```

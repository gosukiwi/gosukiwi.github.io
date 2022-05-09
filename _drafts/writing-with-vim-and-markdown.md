---
layout: post
title:  "Writing with Vim and Markdown"
date:   2022-04-26
categories: vim
---
There are many ways to write text in Vim, in this post in particular I'll talk
about writing Markdown, specifically blog posts, but you can apply this to any
natural language and format.

## A markdown configuration
We'll start building our configuration for writing markdown files. This will
be our starting point:

```vimscript
augroup markdown
  autocmd!
augroup END
```

An empty autocommand group. See `:help 40.3` for a basic explanation on
autocommands, or just `:help autocommands` for the full exhaustive
documentation.

## Spellcheck
One of the most important features for writing is spellcheck. It might not be
as good as, say, Microsoft Word, but it gets the job done, and it's great for
catching typos.

You 

## Autocomplete
You can use `zg` to add a word, and from insert mode, `C-s C-s` will try to
autocomplete the current word.

## Preview
Vim shines at integrating with external programs. We can use
[Pandoc](https://pandoc.org/) to compile Markdown files to HTML.  All we need
to do then is open those files to have our preview:

```vim
function! s:MarkdownPreview() abort
  let filename = tempname() . '.html'
  call system('pandoc "' . expand('%:p') . '" -f markdown -t html -s -o ' . filename)
  call system(s:OpenCommand() . ' ' . filename)
endfunction

function! s:OpenCommand() abort
  if exists("$WSLENV")
    " Windows WSL
    return "cmd.exe /c start /b"
  elseif executable('cmd.exe')
    " Windows
    return "start /b explorer"
  elseif executable("xdg-open")
    " Linux/BSD
    return "xdg-open"
  elseif executable("open")
    " macOS
    return "open"
  endif
endfunction

augroup markdown
  autocmd!
  " ...
  autocmd FileType markdown command! Preview call s:MarkdownPreview()
augroup END
```

We now have a `:Preview` command we can use from markdown which will
automatically open our default browser and display our compiled markdown for
us to see.

## Folding

## Fenced Languages

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

## Folding

## Fenced Languages

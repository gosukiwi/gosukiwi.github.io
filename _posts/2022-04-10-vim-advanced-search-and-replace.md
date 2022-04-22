---
layout: post
title:  "Vim Advanced Search and Replace"
date:   2022-04-19 20:52:00 -0300
categories: jekyll update
---
I find search and replace to be a remarkably interesting topic, because it touches on so many Vim concepts.

When I started using Vim, one of the first things I wanted to do was search for some text in my project and replace it with something else.

Unfortunately for me, it was not as simple as I had initially thought :)

In this post I will show how several different ways to search and replace text in Vim. From the obvious, beginner-friendly way, to the more advanced, _vimmish_ way.

## Before we begin: Reading key sequences
I will use Vim's format to describe sequences of keys. That means `<C-r>` means press the `CONTROL` key, and while holding it, press the `r` key. `<CR>` means `ENTER` key, `<Esc>` means `ESCAPE`, `<Space>` means `SPACEBAR`, etc.

## Searching in a single file
Let's start with our most simple use-case: Searching for some text in a single file.

This is quite a common need. In my regular Vimming, I think one of my most used (and favorite) keys is `*`. In NORMAL mode, simply press `*` on top of a word, and Vim will highlight all the instances of that word in your current buffer.
If you want to go to the next instance, press `n`, if you want to go back, press `N`.

I use that a lot for typos, if I want to make sure I didn't make a typo on some word, and I know that word is already defined somewhere in the same file, I quickly press `*` to see if other words highlight.

<script id="asciicast-488618" src="https://asciinema.org/a/488618.js" async></script>

It's also useful to quickly navigate to all instances of that word, for example, navigate through all the usages of some variable in your current file.

### Section Help
* `:help 03.8`
* `:help *`
* `:help n`

## Replacing in a single file: One by one
Another quite common need is to rename an identifier. This can be tricky when the variable exists in multiple files, but for local variables, it's amazingly easy.

You can use the `.` operator to _repeat the last thing I did in INSERT mode_, together with `*` and `n` you can easily do fine-grained replacements.

<script id="asciicast-488620" src="https://asciinema.org/a/488620.js" async></script>

With this approach you can skip some matches, so it's great when you don't want to just replace all matches, and instead want to choose which ones you want to operate on.

### Section Help
* `:help 04.3`

## Replacing in a single file: Bulk
Alternatively, you can replace all matches in the file in one go, with the `:substitute` command, or just `:s`, as it's normally used.

Without flags, `:substitute` will only replace one match per line. Most of the time, the `g` flag is passed, to substitute all matches. You can also pass the `c` flag so Vim asks for confirmation before doing each replacement.

```vimscript
:%s/this/that/gc
```

If you already were searching for something using `/` or `?`, then Vim populates the search register for you. When using `:s`, and the search string is empty, Vim will use the search register to perform the replacement.

For example, you were searching for all instances of the word `this` by typing `/this`. You then notice you want to change them.

You can now simply type `:%s//that/` to run the replacement. Vim "remembers" our last search.

You can also manually use the register by typing `<C-r>` followed by the register you want to use, such as `"`:

```
:%s/<C-r>"/replacement/g
```

### Section help
* `:help registers`
* `:help 10.2`
* `:help i_CTRL-R`

## Searching in multiple files
To search across multiple files, Vim provides the `:vimgrep` command.

It works out of the box. For example, you can run the following command to search for all instances of `SomeModelClass` inside the `app/models` directory:

```
:vimgrep SomeModelClass app/models
```

Nice! By default, Vim will populate the _quickfix_ list with the search results, but it won't display it. You can open the quickfix window with the `:copen` command.

While that works, it's not the best. You see `:vimgrep` uses Vim's internal implementation of grep, which is totally compatible but terribly slow. Not really what you want to use for most modern software projects (*ahem* `node_modules` *ahem*).

Experienced Vim users will instead use something like [ripgrep](https://github.com/BurntSushi/ripgrep) or [ag](https://github.com/ggreer/the_silver_searcher) for searching across multiple files.

Being the good first-class UNIX citizen that Vim is, it integrates nicely with external programs, so we can easily set this up:

```vimscript
set grepprg=rg\ --vimgrep\ --smart-case
```

We can now use the `:grep` command to search in our files, using `ripgrep`!

```vimscript
:grep myvariable app/models/
```

### Section help
* `:help :vimgrep`
* `:help :grep`
* `:help grepprg`
* `:help quickfix`

## Replacing in multiple files
Once you have performed your search, and have the quickfix list populated with all your matches, you can then run a replacement with:

```
:cfdo %s/pattern/replacement/g
```

The `:cfdo` command will take each file in your quickfix list and apply a command to it. We use the `:substitute` command (a.k.a `:s`) to do the actual replacement.

That's it! This approach might seem complicated to non-Vim users, but it is made of smaller pieces, composed together to create one big action.

Being composable, I can replace commands here and there. For example, instead of replacing, I could actually delete all lines containing the match, with `:global`:

```
:cfdo %g/<my-grep-pattern>/d
```

### Section help
* `:help :cfdo`
* `:help :cdo`
* `:help :global`

## Filtering results
What if you want to make a replacement, but only to __some__ of the files in your quickfix list? Do you run a second, more specific search?

No need! Vim provides a plugin named `cfilter`, which can helps us in this case. You can use it by adding this to your `.vimrc`:

```vimscript
packadd! cfilter
```

Check it out with `:help cfilter`. It gives you a nifty little command to filter the results of the quickfix list: `:Cfilter`:

```
:Cfilter app/models  # display only entries matching `app/models`
:Cfilter! .swp       # remove entries matching `.swp`
```

That's great, and most of the time, it's just enough. But sometimes, it might make sense to cherry-pick the entries you want to keep, by going one by one over them. For that, I have mapped `x` to remove the entry under the cursor:

```vimscript
function! s:QfRemoveAtCursor() abort
  let currline = line('.')
  let items = getqflist()->filter({ index -> (index + 1) != currline })
  call setqflist(items, 'r')
  execute 'normal ' . currline . 'G'
endfunction

augroup quickfix
  autocmd!
  autocmd FileType qf nnoremap <buffer><silent> x :call <SID>QfRemoveAtCursor()<CR>
augroup END
```

Now whenever I press `x` on top of an entry in the quickfix list window, it gets deleted.

Trimming the quickfix list like this is useful, not only to find what you are looking for, but to perform batch operations on all matches with `:cdo` and `:cfdo`!

### Section help
* `:help usr_40.txt`
* `:help usr_41.txt`

## :Grep
Remember we had to manually run `:copen` to see the quickfix list after every
`:grep`? Let's make that automatic by creating a custom `:Grep` command to
open the quickfix list for us:

```vimscript
function! s:Grep(...) abort
  let pattern = get(a:, 1, '')
  if pattern == '' | return | endif

  let path = get(a:, 2, '.')
  execute 'silent! grep! "' . escape(pattern, '"-') . '" ' . path . ' | redraw! | copen'
endfunction

command! -nargs=+ -complete=file Grep silent! call s:Grep(<f-args>)
```

Neat! Now all we have to do is use `:Grep` instead of `:grep`! We even get file autocompletion for grep's optional second parameter.

### Section help
* `:help usr_40.txt`
* `:help key-mapping`
* `:help mapleader`
* `:help i_CTRL-R`

## :Replace
Following on `:Grep`, let's implement a `:Replace` command, so our whole search and replace quest can be reduced to just running two easy to remember commands.

```vimscript
if !exists('s:latest_greps')
  let s:latest_greps = {}
endif

function! s:Grep(...) abort
  let pattern = get(a:, 1, '')
  if pattern == '' | return | endif

  let s:latest_greps[pattern] = 1
  let path = get(a:, 2, '.')
  execute 'silent! grep! "' . escape(pattern, '"-') . '" ' . path . ' | redraw! | copen'
endfunction

function! s:Replace(original, replacement) abort
  if a:original == '' || a:replacement == '' | return | endif

  execute 'cfdo %s/' . escape(a:original, '/') . '/' . a:replacement . '/ge'
endfunction

function! LatestGreps(ArgLead, CmdLine, CursorPos)
  return keys(s:latest_greps)
endfunction

command! -nargs=+ -complete=file Grep silent! call s:Grep(<f-args>)
command! -nargs=+ -complete=customlist,LatestGreps Replace silent! call s:Replace(<f-args>)

nnoremap <Leader>g :Grep<Space>
nnoremap <silent> <Leader>r :call feedkeys(':Replace<Space><Tab>', 't')<CR>
```

Our `:Grep` now stores a list of previous searches, so it can then be used as autocompletion for `:Replace`.

`:Replace` itself simply runs `:cfdo` with a `:substitute` command. It takes two arguments: a search string, and a replacement string. The search string is autocompleted, so we don't have to worry about it.

Finally we map `<Leader>g` to the `:Grep` command, and `<Leader>r` to the `:Replace` command.

When searching, all we need to do now is press `<Leader>g`, and type what we want. For replacing, we simply press `<Leader>r` and type our replacement.

<script id="asciicast-488616" src="https://asciinema.org/a/488616.js" async></script>

Now we're talking! We can even filter the quickfix list in any way we want before doing our replacement. Or we could make the replacement confirm on each match, if we wanted to, using the `c` flag of the `:substitute` command.

You can also run all the search and replace machinery in a single file if you want, too. Simply do `:Grep my-pattern %` to search in the current buffer.

### Section help
* `:help usr_40.txt`
* `:help usr_41.txt`

## Conclusion
In this post I showed several ways to search and replace text in Vim.

It might be more complex than in regular editors, but that complexity has several advantages. Composability being one of the big ones.

Composability is the way of Vim. It allows you to compose several small atoms together, through a "Vim language", into something bigger and more complex. It gives Vim users a lot of power, and it's incredibly fun to learn :)

Every new thing you learn in Vim adds a lot to your final user experience, because you can compose it with all the other tools you know.

Let me know of any feedback you might have - I'm still learning Vim :) - and I hope you find this useful!

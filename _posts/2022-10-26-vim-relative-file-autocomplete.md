---
layout: post
title:  "Vim Relative File Autocomplete"
date:   2022-10-26
categories: vim
---

Vim's file autocomplete (`<C-x><C-f>`) works only for absolute paths (using
the current working directory) as root.

But in most languages, you want to use relative imports, and file autocomplete
doesn't work there. For that, I created the following `<C-x><C-x><C-f>`
mapping:

```vimscript
function! s:EnableRelativeAutocomplete() abort
  let b:relative_autocomplete_cleanup_pending = 1
  lcd %:p:h
endfunction

function! s:DisableRelativeAutocomplete() abort
  if exists('b:relative_autocomplete_cleanup_pending') && b:relative_autocomplete_cleanup_pending
    lcd -
    let b:relative_autocomplete_cleanup_pending = 0
  endif
endfunction

inoremap <C-x><C-x><C-f> <C-o>:call <SID>EnableRelativeAutocomplete()<CR><C-x><C-f>

augroup relative_file_autocomplete
  autocmd!
  autocmd InsertLeave * call s:DisableRelativeAutocomplete()
augroup END
```

You can simply copy-paste that into your `.vimrc`. With that in place, you can
use relative file autocomplete by using `<C-x><C-x><C-f>`. It will work just
like `<C-x><C-f>` does.

It does the trick by changing the current working directory to your current
file's directory when using that mapping, and changing back to the previous
directory when you leave insert mode.

<script id="asciicast-nQEmOTdVQIWv0mJfsLTxFETTg" src="https://asciinema.org/a/nQEmOTdVQIWv0mJfsLTxFETTg.js" async></script>

Hope you find it as useful as I do!

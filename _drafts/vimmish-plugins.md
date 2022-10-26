---
layout: post
title:  "vimmish-plugins"
date:   2022-04-29
categories: vim plugins
---
It's common for Vim newcomers to get called out by experienced Vim users when
they install lots and lots of plugins.

It's not like plugins are _bad_, they are awesome, but some plugins just force
you to do things in a way which is not quite compatible with Vim, and that
might hurt you along the way, particularly if you are new to Vim.

But what does "compatible" mean exactly?

# Extend & Compose
Good plugins **extend** Vim's default functionality. Functionality that is
already there for you.

A great example are custom text objects (`:help text-objects`). The
[vim-textobj-user](https://github.com/kana/vim-textobj-user) plugin provides a
very easy way to define your own text objects.

There are lots of awesome custom text objects out there. The ones I personally
use are `kana/vim-textobj-entire` for selecting the entire file,
`kana/vim-textobj-indent` for the current indentation.

By the way, if you are interested in custom text objects, you might want to
[check out the official wiki
page](https://github.com/kana/vim-textobj-user/wiki) which lists all of the
available text objects users have created.

Let me show you how custom text objects extend Vim's functionality:

```
const aFunction = () => {
  |console.log('Hello,')
  console.log('World!')
}
```

The cursor is represented by `|`. When I'm there, I can say `cii` for `change
inside indentation`. In this case "inside indentation" is provided by the
plugin. That allows me to easily change the body of the function.

In the example above, the plugin not only extends Vim's functionality, by
providing the _indentation_ text object, but it also allows me to **compose**
it with Vim's built-in functionality, such as the `change` command.

That's a great example of a plugin that integrates perfectly into Vim, and it
does things _the Vim way_.

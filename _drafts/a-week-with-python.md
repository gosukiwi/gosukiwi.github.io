---
layout: post
title:  "A week with Python"
date:   2022-04-29
categories: webdev python
---
I've been dabbling into [Python](https://www.python.org/) lately. It was a
great fit for a personal project of mine.

I'm a full-stack senior web developer, working mostly with Rails and Node at
the moment. But I also dabble into programming languages (parsers, compilers)
and game development.

Being a lover of the [Ruby](https://www.ruby-lang.org/en/) programming
language, I never really thought about trying Python. I thought "meh they are
very similar, any is fine". And while that is true to some extent, they are
also quite opposite in other aspects. And that is a good thing :)

After trying Python for a little bit, I think it's an useful tool to have in
your toolbelt. In this post, I'll elaborate why, while I comment on the
similarities and differences with Ruby, and to some extent, JavaScript.

## Elegance
Python is great, but the thing I miss the most about Ruby is it's elegance and
sense of aesthetic.

I might be biased, as I have spent more time reading Ruby code than Python
code, but to me, Ruby feels more expressive and elegant than Python.

That is not without consequences though. The main drawback of using too much
"ruby magic" is that code can get quite implicit and hard to follow.

TODO: EXAMPLE

## Syntax
Python likes to do things with their syntax. For example:

```python
if 1 in [1, 2, 3]:
    do_something()

with open('file', 'r') as f:
    text = f.read()
```

While in Ruby, you rely on method calls and _code blocks_:

```ruby
if [1, 2, 3].include?(1)
  do_something()
end

File.open('file.txt', 'r') do |f|
  text = f.read()
end
```

Python allows you to implement several "magic" or "dunder" methods so you can
make your own objects play nice with Python's syntax. That is pretty cool and
most of the time, it's the _pythonic_ way.

I must admit though that I find Ruby's way more elegant though, as it allows
for more flexibility and same level of readability with less syntactic rules.

That being said, Ruby's syntax is way more complex than Ruby, and implicitness
it built-in, which can be a pain sometimes.

## Windows Support
While there are ways to run Ruby (and even Rails!) on Windows, it has always
been a second class citizen there.

For example, as a professional Ruby developer, you are expected to swap
between different versions to accommodate the needs of your many different
projects, reproduce bugs, perform safe upgrades, etc.

In Linux and macOS, you have tools like `rvm` and `rbenv`. There's no such
thing for Windows.

So I was really surprised to learn that
[pyenv](https://github.com/pyenv-win/pyenv-win) cares about Windows. And not
just them, the Python community as a whole seems to care much more about
Windows. Which IMO is a good thing!

I work with macOS and Linux, but I'm a gamer, my personal computer runs
Windows 11.

As a developer, I like having side projects from time to time, and I like to
do them in my personal computer.

My solution in that case was simple, just use Node. Node has much better
Windows support than Ruby. But it's pleasant to know that I also have another
alternative: Python.

Python can even generate executables, which is massive for easily distributing
software in Windows.

## Bundling and Packaging
In Ruby, you have `gem` and `bundler`, and that's it. Ruby's `gem` command
will install a gem, and `bundler` manages dependencies and versions.

In Python, you have `pip`, which is the equivalent of `gem`, but then you have
lots of different ways to manage packages. `pipenv` would be the closest
equivalent of `bundler`, and what I use at the moment. Haven't had any issue
with it so far, does what it says in the tin.

When starting out, it's not immediately clear which of the several solutions
you should use, but that's fine. It can also be a good thing, not putting all
your eggs in a single basket.

There are efforts to tackle this, particularly
[Poetry](https://python-poetry.org/), although I haven't tried it yet.

## Absolute Imports
In Rails, you don't have to worry about imports, but if you manually write
Ruby code, you'll have to import your own files. That is not a big deal, each
file simply references other files using relative paths. Same as in
JavaScript.

When running Ruby code, you do as you would expect:

```
$ ruby my_file.rb
```

Python is... different. In Python, to do the same you'd do in Ruby or Node,
you have to do:

```
$ python -m my_file
```

Python prefers absolute imports. The whole import thing was a pain in the ass
to get used to, but once you get used to it, it's not a big deal.

## Google Fu
Ruby is a mature language, and most search results are Stack Overflow posts,
or GitHub issues.

For Python, most results are marketing websites and low quality tutorials and
courses. Sometimes even behind paywalls. Not like it's Python's fault, though.

## Libraries
Ruby is really mature and has lots of awesome libraries, and so is Python. I
think in this aspect, Python wins by a small margin, given it's more popular,
and has libraries that Ruby doesn't, like making Pyinstaller.

## Metaprogramming
I'd give Ruby the small edge over this, but Python is surprisingly powerful
for such a simple syntax.

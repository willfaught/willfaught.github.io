---
date: '2013-04-27T14:54:53-07:00'
tags:
- computation
title: Homebrew And The OS X $PATH
---

I recently installed Homebrew on OS X and observed that the Homebrew binary path was added to the end of `$PATH` instead of the beginning, so even though I installed Python with Homebrew, `python` still resolved to the OS X version. Instead of fixing this in my `~/.profile` with prepending the Homebrew path to `$PATH`, I edited `/etc/paths` and moved the Homebrew path to the first line. Now `$PATH` always has the Homebrew path first. This is convenient for working in Terminal, but I have to cross my fingers that nothing in OS X breaks.

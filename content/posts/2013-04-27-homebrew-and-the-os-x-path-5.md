+++
date = "2013-04-27T21:54:53+00:00"
draft = false
tags = ["computation"]
title = "Homebrew and the OS X $PATH"
+++
I recently installed Homebrew on OS X and observed that the Homebrew binary path was added to the end of `$PATH` instead of the beginning, so even though I installed Python with Homebrew, `python` still resolved to the OS X version.

Instead of fixing this in my `~/.profile` with prepending the Homebrew path to `$PATH`, I edited `/etc/paths` and moved the Homebrew path to the first line. Now `$PATH` always has the Homebrew path first.

This is convenient for working in Terminal, but I have to cross my fingers that nothing in OS X breaks.

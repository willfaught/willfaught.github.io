---
date: '2016-12-07T07:06:43+00:00'
draft: false
tags:
- programming
- c
- calloc
title: Why does calloc exist?
---

From https://vorpus.org/blog/why-does-calloc-exist/:

Nathaniel J. Smith on the purpose of calloc:

>When the operating system hands out memory to a process, it always zeros it out first, because otherwise our process would be able to peek at whatever detritus was left in that memory by the last process to use it, which might include, like, crypto keys, or embarrassing fanfiction. So that’s the first way that calloc cheats: when you call malloc to allocate a large buffer, then probably the memory will come from the operating system and already be zeroed, so there’s no need to call memset. But you don’t know that for sure! Memory allocators are pretty inscrutable. So you have to call memset every time just in case. But calloc lives inside the memory allocator, so it knows whether the memory it’s returning is fresh from the operating system, and if it is then it skips calling memset. And this is why calloc has to be built into the standard library, and you can’t fake it yourself.

Never knew.

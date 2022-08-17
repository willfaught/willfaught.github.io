---
date: '2015-10-17'
tags:
- libevent
- libev
- libuv
title: libevent vs. libev vs. libuv
---

What's the difference between libevent, libev, and libuv?

[Marc Lehmann on Stack Overflow](http://stackoverflow.com/questions/9433864/whats-the-difference-between-libev-and-libevent):

>libev was created to improve on some of the architectural decisions in libevent, for example, global variable usage made it hard to use libevent safely in multithreaded environments, watcher structures are big because they combine I/O, time and signal handlers in one, the extra components such as the http and dns servers suffered from bad implementation quality and resultant security issues, and timers were inexact and didn't cope well with time jumps.

>Libev tried to improve each of these, by not using global variables but using a loop context for all functions, by using small watchers for each event type (an I/O watcher uses 56 bytes on x86_64 compared to 136 for libevent), allowing extra event types such as timers based on wallclock vs. monotonic time, inter-thread interruptions, prepare and check watchers to embed other event loops or to be embedded and so on.

[Nikhil Marathe on GitHub](https://nikhilm.github.io/uvbook/introduction.html):

>The node.js project began in 2009 as a JavaScript environment decoupled from the browser. Using Google’s V8 and Marc Lehmann’s libev, node.js combined a model of I/O – evented – with a language that was well suited to the style of programming; due to the way it had been shaped by browsers. As node.js grew in popularity, it was important to make it work on Windows, but libev ran only on Unix. The Windows equivalent of kernel event notification mechanisms like kqueue or (e)poll is IOCP. libuv was an abstraction around libev or IOCP depending on the platform, providing users an API based on libev. In the node-v0.9.0 version of libuv libev was removed.

So each one succeeds the previous one. Libuv is the hot stuff today.

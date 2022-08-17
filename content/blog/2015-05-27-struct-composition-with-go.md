---
date: '2015-05-26'
tags:
- golang
title: Struct composition with Go
---

From http://dave.cheney.net/2015/05/22/struct-composition-with-go?utm_source=golangweekly&utm_medium=email:

Dave Cheney on bridging from one Golang interface to another:

>The value returned by io.MultiWriter is an implementation of io.Writer, it doesn’t have the rest of the methods necessary to fulfil the net.Conn interface; what I really need is the ability to replace the Write method of an existing net.Conn value. We can do this with embedding by creating a structure that embeds both a net.Conn and an independant io.Writer as anonymous fields.

---
date: "2014-06-06T18:20:04+00:00"
draft: false
tags: ["computing"]
title: "Go isn't so parallel after all"
---

[Effective Go](http://golang.org/doc/effective_go.html):

>The current implementation of the Go runtime will not parallelize this code by default. It dedicates only a single core to user-level processing. An arbitrary number of goroutines can be blocked in system calls, but by default only one can be executing user-level code at any time. It should be smarter and one day it will be smarter […]

So, at best, the default Go runtime can be called non-blocking, but not parallel. I wonder how fair time sharing among goroutines is, if it is at all? Can an infinite loop in a goroutine hog the runtime?

>Either run your job with environment variable GOMAXPROCS set to the number of cores to use or import the runtime package and call runtime.GOMAXPROCS(NCPU). A helpful value might be runtime.NumCPU(), which reports the number of logical CPUs on the local machine.

Why doesn't that happen by default?


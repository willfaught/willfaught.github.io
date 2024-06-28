+++
categories = ["life"]
date = 2008-04-08T18:23:00-07:00
tags = []
title = "The State Of Operating Systems"
+++

Today's operating systems are horribly out of date. They all follow the same process and file descriptor model and never really change. They may add new services or interfaces over time, but never do their core program semantics change enough to break backwards compatibility.

They all follow the process and file descriptor model, where processes are conceptually functions that map input values to output values and file descriptors conceptually compose functions by connecting function outputs to function inputs (also known as filtering). Functions and function composition are powerful constructs, but their underlying mechanisms are less than ideal. Functions can have only one input parameter (standard input). File descriptors force users to do the heavy lifting of reading and writing data. Software components are isolated in separate processes that prevent them from being reused.

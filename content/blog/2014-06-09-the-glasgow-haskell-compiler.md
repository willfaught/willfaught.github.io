---
date: '2014-06-08'
tags:
- computing
title: The Glasgow Haskell Compiler
---

From http://www.aosabook.org/en/ghc.html:

An interesting overview of the **Glasgow Haskell Compiler**. I had no idea about some of this stuff.

On optimizing the performance of trivial functions:

>If every use of such a function really required a function call, efficiency would be terrible. One solution is to make the compiler treat certain functions specially; another is to use a pre-processor to replace a "call" with the desired inline code. All of these solutions are unsatisfactory in one way or another, especially as another solution is so obvious: simply inline the function. To "inline a function" means to replace the call by a copy of the function body, suitably instantiating its parameters.

>In GHC we have systematically adopted this approach [PM02]. Virtually nothing is built into the compiler. Instead, we define as much as possible in libraries, and use aggressive inlining to eliminate the overheads. This means that programmers can define their own libraries that will be inlined and optimised as well as the ones that come with GHC.

On rewrite rules (basically, macros):

>The entire rule is a pragma, introduced by {-# RULES. The rule says that whenever GHC sees the expression (foldr k z (build g)) it should rewrite it to (g k z). This transformation is semantics-preserving, but it takes a research paper to argue that it is [GLP93], so there is no chance of GHC performing it automatically. Together with a handful of other rules, and some INLINE pragmas, GHC is able to fuse together list-transforming functions. For example, the two loops in (map f (map g xs)) are fused into one.

On lightweight threads:

>Green threads, otherwise known as lightweight threads or user-space threads, are a well-known technique for avoiding the overhead of operating system threads. The idea is that threads are managed by the program itself, or a library (in our case, the RTS), rather than by the operating system. Managing threads in user space should be cheaper, because fewer traps into the operating system are required.

>In the GHC RTS we take full advantage of this idea. A context switch only occurs when the thread is at a safe point, where very little additional state needs to be saved. Because we use accurate GC, the stack of the thread can be moved and expanded or shrunk on demand. Contrast these with OS threads, where every context switch must save the entire processor state, and where stacks are immovable so a large chunk of address space has to be reserved up front for each thread.

It's a great read if you know Haskell. It tries to be understandable by the layperson too and does a pretty good job.

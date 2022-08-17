---
date: '2016-07-13'
link: https://aphyr.com/posts/283-call-me-maybe-redis
tags:
- aphyr
- redis
- jepsen
- database
- programming
- coding
title: Aphyr tests Redis with Jepsen
---

Aphyr on Redis correctness:

>So you’re running a distributed Redis install, and have realized that the design of Redis Sentinel (or, for that matter, any other failover system on top of an asynchronously replicated primary-secondary design) means you can lose a lot of data when a partition occurs. What can you do?
>
>From an operations perspective, I recommend you try to understand the Sentinel consensus algorithm. I don’t, and I’ve read it a dozen times.

LOL.

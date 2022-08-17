---
date: '2013-03-22T01:35:47+00:00'
draft: false
tags:
- computation
title: The beauty of XOR
---

The beauty of XOR is that it's commutative and doing the same XOR twice cancels it out: `1 XOR 0 XOR 1 = 0`. `1 XOR 1 XOR 0 = 0`. The solution to this problem is to XOR all the integers together: Given an unsorted integer array, where every integer occurs twice except one, which occurs once, find the single integer.

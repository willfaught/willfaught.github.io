---
date: '2013-01-06T18:02:00-08:00'
tags:
- computation
title: Compute the middle index
---

Java: int left = ...; int right = ...; int middle = (right - left) / 2 + left; Alternatively: int middle = (left + right) / 2; Proof: (right - left) / 2 + left = (left + right) / 2 right - left + left * 2 = left + right right + left = left + right The second method is much simpler to understand and compute.

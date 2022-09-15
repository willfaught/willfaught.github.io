---
date: '2013-01-31T00:01:00-08:00'
tags:
- computation
title: Longest Contiguous Subarray
---

Longest contiguous subarray of a signed int array that sums to zero or greater: int[] longest(int[] a) { if (a == null) return null; int longestIndex = -1, longestLength = 0; for (int i = 0; i < a.length; ++i) { int sum = 0; int length = 0; for (int j = i; j < a.length; ++j) { sum += a[j]; ++length; if (sum >= 0 && length > longestLength) { longestIndex = i; longestLength = length; } } } if (longestIndex == -1) return null; int[] b = new int[longestLength]; for (int i = 0; i < longestLength; ++i) b[i] = a[i + longestIndex]; return b; } I realize there's a similar O(*n* log *n*) algorithm by Jon Bentley, but I haven't studied it yet.

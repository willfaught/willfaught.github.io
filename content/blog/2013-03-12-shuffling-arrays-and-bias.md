---
date: '2013-03-11'
link: http://adrianquark.blogspot.com/2008/09/how-to-shuffle-array-correctly.html
tags:
- computation
title: Shuffling arrays and bias
---

Adrian Quark proving the Fisher–Yates shuffle is unbiased:

>Only later did I prove to myself that the current-or-later algorithm works. Think about the last element in an array of size n. What are the odds that it ends up at index 1? Obviously, 1/n. What about index 2? This is a bit trickier, since it's a compound probability: we have to multiply the odds of not swapping with 1 by the odds of then swapping with 2, to find the probability of the element actually ending up at 2. In other words, (n-1)/n * 1/(n-1) = 1/n. Similar math shows that there is an equal probability of swapping with 3, 4, etc, and you can do the same for other elements in the array besides the last.

There's also a proof of why swapping with any other index is biased.

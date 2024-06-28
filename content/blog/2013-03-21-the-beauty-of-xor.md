+++
date = 2013-03-21T18:35:47-07:00
tags = ["computation"]
title = "The Beauty Of XOR"
+++

The beauty of XOR is that it's commutative and doing the same XOR twice cancels it out: `1 XOR 0 XOR 1 = 0`. `1 XOR 1 XOR 0 = 0`. The solution to this problem is to XOR all the integers together: Given an unsorted integer array, where every integer occurs twice except one, which occurs once, find the single integer.

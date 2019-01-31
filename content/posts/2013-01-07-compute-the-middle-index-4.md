+++
date = "2013-01-07T02:02:00+00:00"
draft = false
tags = ["computation"]
title = "Compute the middle index"
+++
Java:

    int left = ...;
    int right = ...;
    int middle = (right - left) / 2 + left;

Alternatively:

    int middle = (left + right) / 2;

Proof:

    (right - left) / 2 + left = (left + right) / 2
    right - left + left * 2 = left + right
    right + left = left + right

The second method is much simpler to understand and compute.
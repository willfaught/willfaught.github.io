+++
date = "2013-03-22T00:41:11+00:00"
draft = false
tags = ["computation"]
title = "Simulate a die roll with coin tosses"
+++
Given an unbiased coin toss function, implement an unbiased die roll function.

You can't. Here's an incorrect solution: Toss the coin three times. There are eight possible outcomes. If you get one of the first six possible outcomes, then use that to represent the die roll. If you get the seventh or eighth possible outcome, then try again.

This solution ignores the premise that the coin toss is unbiased, and thus it isn't guaranteed to halt in the case that you keep getting the seventh or eighth possible outcomes. It will probably halt eventually in practice, but you also wouldn't solve it this way in practice either.

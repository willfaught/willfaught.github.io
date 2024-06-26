+++
date = 2017-07-13T16:58:11-07:00
link = "https://www.cockroachlabs.com/blog/rounding-implementations-in-go/"
tags = ["go", "go", "programming"]
title = "Survey Of Rounding Implementations In Go"
+++

>Rounding in Go is hard to do correctly. That is, given a float64, truncate the fractional part (anything right of the decimal point), and add one to the truncated value if the fractional part was >= 0.5. This problem doesn't come up often, but it does enough that as of this writing, the second hit on Google for golang round is a [closed issue](https://github.com/golang/go/issues/4594) from the Go project, which declined to add a Round function to the math package.

Of course it did. Sigh.

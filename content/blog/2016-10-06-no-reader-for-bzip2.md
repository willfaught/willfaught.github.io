+++
date = 2016-10-06T23:09:31-07:00
tags = ["go"]
title = "No Reader For Bzip2"
+++

Apparently compress/bzip2 is the only Golang compression standard library that doesn't have a Writer counterpart for its Reader. Lame.

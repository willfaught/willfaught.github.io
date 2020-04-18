---
date: "2015-09-02T21:25:08+00:00"
draft: false
tags: ["golang"]
title: "Go GC: Prioritizing low latency and simplicity"
---
From http://blog.golang.org/go15gc:

Go blog:

>Go 1.5’s GC ushers in a future where stop-the-world pauses are no longer a barrier to moving to a safe and secure language. It is a future where applications scale effortlessly along with hardware and as hardware becomes more powerful the GC will not be an impediment to better, more scalable software. It’s a good place to be for the next decade and beyond.

Really impressive goals. I love the idea of having only one GC "knob" to turn. Scaling automatically is very smart.
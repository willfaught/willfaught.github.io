---
categories:
- technology
date: '2010-09-17T23:04:00-0700'
tags:
- hard drive
- ubuntu
title: Wiping My Old Hard Drive
---

I'm going to donate my old desktop computer. To be safe, I wanted to wipe the hard drive. I figured an [Ubuntu Live CD](http://www.ubuntu.com/desktop/get-ubuntu/download) would do the trick, and I almost got it to work by guessing the command, but I had mistakenly specified the partition (sda5) instead of the drive itself (sda). It worked like a charm. Here's how I did it:

```sh
sudo swapoff -a
sudo shred -vfz /dev/sda
```

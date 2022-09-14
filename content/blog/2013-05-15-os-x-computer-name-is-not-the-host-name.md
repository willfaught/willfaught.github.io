---
date: '2013-05-15T21:54:17-07:00'
tags:
- computation
title: OS X computer name is not the host name
---

If you configure the OS X computer name in *System Preferences*, *Sharing*, *Computer Name*, but it's not reflected by `/bin/hostname`, then execute `sudo scutil --set HostName MYHOSTNAME` and restart.

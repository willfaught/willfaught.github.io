+++
date = "2013-05-16T04:54:17+00:00"
draft = false
tags = ["computation"]
title = "OS X computer name is not the host name"
+++
If you configure the OS X computer name in *System Preferences*, *Sharing*, *Computer Name*, but it's not reflected by `/bin/hostname`, then execute `sudo scutil --set HostName MYHOSTNAME` and restart.

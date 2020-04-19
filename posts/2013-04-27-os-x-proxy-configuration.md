---
date: "2013-04-27T21:47:00+00:00"
draft: false
tags: ["computation"]
title: "OS X proxy configuration"
---
If you use OS X, and you use an HTTP forward proxy, and you use command line programs like `curl` or `wget` that use the HTTP or HTTPS protocols, then you must set the shell environment variables `http_proxy` and `https_proxy` (and their all-caps equivalents). An example of doing this in `~/.profile`: export http_proxy="proxy.company.com:3128" export https_proxy="proxy.company.com:3128" export HTTP_PROXY=$http_proxy export HTTPS_PROXY=$https_proxy If you don't do this, you'll see errors like this: $ curl http://www.google.com curl: (7) couldn't connect to host I believe that these variables also work for other UNIX operating systems.

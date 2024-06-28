+++
date = 2013-03-09T16:02:00-08:00
tags = ["computation"]
title = "Remember To Shut Down ExecutorService"
+++

Methods that take a `java.util.concurrent.Executor` might not know if it's an `ExecutorService`, so you have to call `ExecutorService.shutdown()` yourself.

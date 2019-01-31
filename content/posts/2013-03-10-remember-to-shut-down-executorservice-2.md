+++
date = "2013-03-10T00:02:00+00:00"
draft = false
tags = ["computation"]
title = "Remember to shut down ExecutorService"
+++
Methods that take a `java.util.concurrent.Executor` might not know if it's an `ExecutorService`, so you have to call `ExecutorService.shutdown()` yourself.

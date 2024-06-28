+++
date = 2013-02-06T17:16:00-08:00
link = "https://www.velocityreviews.com/forums/t149170-why-synchronized-with-wait-notify.html"
tags = ["computation"]
title = "Why Java's Object.wait/notify/notifyAll Must Be Synchronized"
+++

Best explanation I've seen yet of why Java's `Object.wait`/`notify`/`notifyAll` must be synchronized on the same `Object` instance, by Chris Smith:

>`wait()` isn't strictly guaranteed to do anything at all. Something
called "spurious wakeups" might occur. That is, a call to wait() can
return at any time without any good reason. So a good rule of thumb for
using wait() is that you should consider it to be nothing but an
optimization. If a program issn't correct already (and assuming that
thread starvation is not occurring), calling wait() can NOT make a
program correct. However, it certainly can make a program incorrect!

>So we need to start out thinking of how we'd write the code without wait
at all. So how do you you wait for something is wait() doesn't do
anything? Easy... you do a busy-wait:

>`while (!thingImWaitingFor()) /* DO NOTHING */;`

>This uses 100% of the CPU power available, of course, and performance
horribly. The optimization is this:

>`while (!thingImWaitingFor()) wait();`

>That explains the need for a "predicate", which in this case is
`!thingImWaitingFor()`. Next, you need an absolute guarantee that the
waiter and the notifier agree about the state of the predicate. The
waiter checks the state of the predicate at some point slightly BEFORE
it goes to sleep, but it depends for correctness on the predicate being
true WHEN it goes to sleep. There's a period of vulnerability between
those two events, which can break the program.

>You need that period of vulnerability to be protected by a synchronized
block. If the waiter didn't synchronize, then any old bit of code might
change the predicate just before it goes to sleep, and then we're
certainly in trouble. If the notifier didn't synchronize, then it could
change the predicate even though the waiter is holding the lock... and
we'd still have trouble.

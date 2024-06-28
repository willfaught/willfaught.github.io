+++
date = 2013-05-16T19:50:38-07:00
link = "https://mail.python.org/pipermail/python-list/2012-June/624923.html"
tags = ["computation"]
title = "Signatures For Built-In Python Methods"
+++

Ian Kelly:

>Methods implemented in C don't really even
have established argument specs.  They just take tuples and dicts and
use the PyArg_Parse* family of functions to unpack them.  So
effectively, all built-in methods have the argspec (self, *args,
**keywords)

The signature for `Exception.__init__` seems to be `(self, *args)`.

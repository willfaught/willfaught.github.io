---
date: '2013-03-19T15:22:46-07:00'
link: https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html
tags:
- computation
title: Member Variables In Outer Class Scopes
---

>Refer to member variables that enclose larger scopes by the class name to which they belong. For example, the following statement accesses the member variable of the class `ShadowTest` from the method `methodInFirstLevel`:

>`System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);`

Just learned about this. I guess it doesn't come up often.

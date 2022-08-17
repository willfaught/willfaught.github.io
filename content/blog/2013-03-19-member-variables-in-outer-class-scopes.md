---
date: "2013-03-19T22:22:46+00:00"
draft: false
tags: ["computation"]
title: "Member variables in outer class scopes"
---
From http://docs.oracle.com/javase/tutorial/java/javaOO/nested.html:

>Refer to member variables that enclose larger scopes by the class name to which they belong. For example, the following statement accesses the member variable of the class `ShadowTest` from the method `methodInFirstLevel`:

>`System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);`

Just learned about this. I guess it doesn't come up often.

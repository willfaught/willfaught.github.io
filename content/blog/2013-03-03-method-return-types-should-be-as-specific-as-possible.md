---
date: "2013-03-03T20:05:00+00:00"
draft: false
tags: ["computation"]
title: "Method return types should be as specific as possible"
---
From http://docs.oracle.com/javase/tutorial/collections/interoperability/api-design.html:

Josh Bloch:

>In one sense, return values should have the opposite behavior of input parameters: It's best to return the most specific applicable collection interface rather than the most general. For example, if you're sure that you'll always return a SortedMap, you should give the relevant method the return type of SortedMap rather than Map. SortedMap instances are more time-consuming to build than ordinary Map instances and are also more powerful. Given that your module has already invested the time to build a SortedMap, it makes good sense to give the user access to its increased power. Furthermore, the user will be able to pass the returned object to methods that demand a SortedMap, as well as those that accept any Map.

Method parameter types should be as general as possible, and method return types should be as specific as possible.
---
date: '2018-03-10'
tags: null
title: Life of a Go Infrastructure Maintainer
---

From https://medium.com/@zombiezen/life-of-a-go-infrastructure-maintainer-cb1419308eb5:

Hyrum Wright:

>Given enough use, there is no such thing as a private implementation. That is, if an interface has enough consumers, they will collectively depend on every aspect of the implementation, intentionally or not. This effect serves to constrain changes to the implementation, which must now conform to both the explicitly documented interface, as well as the implicit interface captured by usage. We often refer to this phenomenon as “bug-for-bug compatibility.”

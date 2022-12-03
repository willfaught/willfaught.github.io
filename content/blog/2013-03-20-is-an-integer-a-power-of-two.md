---
date: '2013-03-20T17:47:00-07:00'
tags:
- computation
- code
title: Is An Integer A Power Of Two?
---

Java:

```java
boolean power2(int x) {
    return x != 0 && (x & (x - 1)) == 0;
}
```

A power of two must be greater than zero.

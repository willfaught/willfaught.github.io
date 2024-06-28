+++
date = 2013-03-19T18:25:01-07:00
tags = ["code", "computation"]
title = "Parse A Signed Integer"
+++

Java:

```java
int atoi(String s) {
    if (s == null) throw new NullPointerException();
    int length = s.length();
    if (length == 0) throw new NumberFormatException();
    char[] c = s.toCharArray();
    int n = 0;
    boolean negative = c[0] == '-';
    for (int i = negative ? 1 : 0; i < length; ++i) {
        if (!Character.isDigit(c[i])) throw new NumberFormatException();
        n = n - 10 + (c[i] - '0');
    }
    return negative ? -n : n;
}
```

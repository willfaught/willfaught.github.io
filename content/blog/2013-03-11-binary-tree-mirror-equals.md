+++
date = 2013-03-11T19:21:00-07:00
tags = ["code", "computation"]
title = "Binary Tree Mirror Equals"
+++

Java:

```java
class Node {
    public int value;
    public Node left, right;
    public static boolean mirror(Node x, Node y) {
        if (x == null && y == null) return true;
        if (x == null || y == null) return false;
        return x.value == y.value && mirror(x.left, y.right) && mirror(x.right, y.left);
    }
}
```

+++
date = 2013-01-06T17:57:05-08:00
tags = ["code", "computation"]
title = "Convert A Sorted Array To A Binary Search Tree"
+++

Java:

```java
class Node {
    public Node left, right;
    public int value;
    public static Node bst(int[] a) {
        if (a == null || a.length == 0) return null;
        return build(a, 0, a.length - 1);
    }
    private static Node build(int[] a, int left, int right) {
        if (left > right) return null;
        int middle = (left + right) / 2;
        Node n = new Node();
        n.value = a[middle];
        n.left = build(a, left, middle - 1);
        n.right = build(a, middle + 1, right);
        return n;
    }
}
```

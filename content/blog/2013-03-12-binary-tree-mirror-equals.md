---
date: '2013-03-12T02:21:00+00:00'
draft: false
tags:
- computation
- code
title: Binary tree mirror equals
---

Java: class Node { public int value; public Node left, right; public static boolean mirror(Node x, Node y) { if (x == null && y == null) return true; if (x == null || y == null) return false; return x.value == y.value && mirror(x.left, y.right) && mirror(x.right, y.left); } }

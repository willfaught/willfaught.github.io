---
date: '2012-12-28T01:37:00+00:00'
draft: false
tags:
- computation
- code
title: Decide whether a binary tree is balanced
---

Java: class Node { public Node left, right; private static int height(Node n) { if (n == null) return 0; return 1 + Math.max(height(n.left), height(n.right)); } public static boolean isBalanced(Node n) { if (n == null) return true; if (Math.abs(height(n.left) - height(n.right)) > 1) return false; return isBalanced(n.left) && isBalanced(n.right); } }

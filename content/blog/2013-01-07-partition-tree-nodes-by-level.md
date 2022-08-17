---
date: '2013-01-07T06:15:00+00:00'
draft: false
tags:
- computation
- code
title: Partition tree nodes by level
---

Java: class Node { public Node left, right; public LinkedList> traverse() { LinkedList> lists = new LinkedList>(); LinkedList list = new LinkedList(); Queue nodes = new LinkedList(); nodes.offer(this); int parents = 1, children = 0; while (parents > 0) { Node n = nodes.remove(); list.add(n); if (n.left != null) { nodes.offer(n.left); ++children; } if (n.right != null) { nodes.offer(n.right); ++children; } \--parents; if (parents == 0) { lists.add(list); list = new LinkedList(); parents = children; children = 0; } } return lists; } } An even simpler solution: public LinkedList> traverse() { LinkedList> lists = new LinkedList>(); LinkedList parents = new LinkedList(); parents.add(this); while (!parents.isEmpty()) { lists.add(parents); LinkedList children = new LinkedList(); for (Node parent : parents) { if (parent.left != null) children.add(parent.left); if (parent.right != null) children.add(parent.right); } parents = children; } return lists; }

---
date: '2013-01-06'
tags:
- computation
- code
title: Search for a path in a graph
---

Java: class Node { public Node[] adj; public boolean isPathTo(Node target) { if (target == null) return false; Stack s = new Stack(); HashMap m = new HashMap(); s.push(this); while (!s.empty()) { Node n = s.pop(); m.put(n, n); for (Node n2 : n.adj) { if (n2 == target) return true; if (n2 != null && !m.containsKey(n2)) s.push(n2); } } return false; } }

---
date: '2013-01-06T16:31:00-08:00'
tags:
- computation
- code
title: Search For A Path In A Graph
---

Java:

```java
class Node {
    public Node[] adj;
    public boolean isPathTo(Node target) {
        if (target == null) return false;
        Stack<Node> s = new Stack<Node>();
        HashMap<Node, Node> m = new HashMap<Node, Node>();
        s.push(this);
        while (!s.empty()) {
            Node n = s.pop();
            m.put(n, n);
            for (Node n2 : n.adj) {
                if (n2 == target) return true;
                if (n2 != null && !m.containsKey(n2)) s.push(n2);
            }
        }
        return false;
    }
}
```

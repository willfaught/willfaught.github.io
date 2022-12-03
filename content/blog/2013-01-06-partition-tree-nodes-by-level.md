---
date: '2013-01-06T22:15:00-08:00'
tags:
- computation
- code
title: Partition Tree Nodes By Level
---

Java:

```java
class Node {
    public Node left, right;
    public LinkedList > traverse() {
        LinkedList<LinkedList<Node>> lists = new LinkedList<LinkedList<Node>>();
        LinkedList<Node> list = new LinkedList<Node>();
        Queue<Node> nodes = new LinkedList<Node>();
        nodes.offer(this);
        int parents = 1, children = 0;
        while (parents > 0) {
            Node n = nodes.remove();
            list.add(n);
            if (n.left != null) {
                nodes.offer(n.left);
                ++children;
            }
            if (n.right != null) {
                nodes.offer(n.right);
                ++children;
            }
            --parents;
            if (parents == 0) {
                lists.add(list);
                list = new LinkedList<Node>();
                parents = children;
                children = 0;
            }
        }
        return lists;
    }
}
```

An even simpler solution:

```java
public LinkedList > traverse() {
    LinkedList<LinkedList<Node>> lists = new LinkedList<LinkedList<Node>>();
    LinkedList<Node> parents = new LinkedList<Node>();
    parents.add(this);
    while (!parents.isEmpty()) {
        lists.add(parents);
        LinkedList<Node> children = new LinkedList<Node>();
        for (Node parent : parents) {
            if (parent.left != null) children.add(parent.left);
            if (parent.right != null) children.add(parent.right);
        }
        parents = children;
    }
    return lists;
}
```

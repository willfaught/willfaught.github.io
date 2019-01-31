+++
date = "2013-01-07T07:38:00+00:00"
draft = false
tags = ["computation", "code"]
title = "Decide if a binary tree is a binary search tree"
+++
Java:

Recursive solution 1:

    class Node
    {
        public Node left, right;
        public int value;
        private boolean first;
        private int previous;

        public boolean isBST()
        {
            first = true;
            return isBST(this);
        }

        private boolean isBST(Node n)
        {
            if (n == null) return true;
            if (!isBST(n.left)) return false;
            if (first)
            {
                first = false;
                previous = n.value;
            }
            else if (previous &gt;= n.value) return false;
            previous = n.value;
            return isBST(n.right);
        }
    }

Recursive solution 2:

    public boolean isBST()
    {
        return isBST(this, null, null);
    }

    public boolean isBST(Node n, Integer min, Integer max)
    {
        if (n == null) return true;
        if ((min != null &amp;&amp; n.value &lt;= min) || (max != null &amp;&amp; n.value &gt;= max)) return false;
        return isBST(n.left, min, n.value) &amp;&amp; isBST(n.right, n.value, max);
    }

Iterative solution:

    public boolean isBST()
    {
        boolean first = false;
        int previous = 0;
        Stack nodes = new Stack();
        Node n = this;
        while (n != null)
        {
            nodes.push(n);
            n = n.left;
        }
        while (!nodes.empty())
        {
            n = nodes.pop();
            if (first) first = false;
            else if (previous &gt;= n.value) return false;
            previous = n.value;
            n = n.right;
            while (n != null)
            {
                nodes.push(n);
                n = n.left;
            }
        }
        return true;
    }

+++
date = 2013-03-21T17:02:01-07:00
tags = ["code", "computation"]
title = "Stack That Tracks The Minimum Element"
+++

Java:

```java
class MinStack<E> {
    private Stack<E> elements = new Stack<E>();
    private Stack<E> mins = new Stack<E>();
    public void push(E element) {
        elements.push(element);
        if (mins.isEmpty() || mins.peek().compareTo(element) > 0) mins.push(element);
    }
    public E pop() {
        if (elements.isEmpty()) throw new EmptyStackException();
        E element = elements.pop();
        if (mins.peek().compareTo(element) == 0) mins.pop();
        return element;
    }
    public E min() {
        if (mins.isEmpty()) throw new EmptyStackException();
        return mins.peek();
    }
}
```

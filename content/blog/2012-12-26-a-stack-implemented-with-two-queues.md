---
date: '2012-12-26T23:49:00-08:00'
tags:
- computation
- code
title: A Stack Implemented With Two Queues
---

Java:

```java
public class DoubleQueueStack<E> {
    private LinkedList<E> store = new LinkedList<E>(), other = new LinkedList<E>();
    public void push(E e) {
        store.offer(e);
    }
    public E pop() {
        if (store.isEmpty()) throw new IllegalStateException();
        E e = null;
        while (!store.isEmpty()) {
            e = store.remove();
            if (store.isEmpty()) break;
            other.offer(e);
        }
        LinkedList<E> t = store;
        store = other;
        other = t;
        return e;
    }
    public boolean isEmpty() {
        return store.isEmpty();
    }
}
```

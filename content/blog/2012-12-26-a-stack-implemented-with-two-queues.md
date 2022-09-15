---
date: '2012-12-26T23:49:00-08:00'
tags:
- computation
- code
title: A Stack Implemented With Two Queues
---

Java: public class DoubleQueueStack { private LinkedList store = new LinkedList(), other = new LinkedList(); public void push(E e) { store.offer(e); } public E pop() { if (store.isEmpty()) throw new IllegalStateException(); E e = null; while (!store.isEmpty()) { e = store.remove(); if (store.isEmpty()) { break; } other.offer(e); } LinkedList t = store; store = other; other = t; return e; } public boolean isEmpty() { return store.isEmpty(); }

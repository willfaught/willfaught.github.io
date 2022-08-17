---
date: '2012-12-19'
tags:
- computation
- code
title: A queue implemented with two stacks
---

Java: public class DoubleStackQueue { private Stack enqueue = new Stack(), dequeue = new Stack(); private void move(Stack from, Stack to) { while (!from.empty()) to.push(from.pop()); } public void enqueue(E element) { if (!dequeue.empty()) move(dequeue, enqueue); enqueue.push(element); } public E dequeue() { if (isEmpty()) throw new IllegalStateException(); if (!enqueue.empty()) move(enqueue, dequeue); return dequeue.pop(); } public boolean isEmpty() { return enqueue.empty() && dequeue.empty(); } }

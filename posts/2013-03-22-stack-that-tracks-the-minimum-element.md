---
date: "2013-03-22T00:02:01+00:00"
draft: false
tags: ["computation", "code"]
title: "Stack that tracks the minimum element"
---
Java: class MinStack> { private Stack elements = new Stack<>(); private Stack mins = new Stack<>(); public void push(E element) { elements.push(element); if (mins.isEmpty() || mins.peek().compareTo(element) > 0) mins.push(element); } public E pop() { if (elements.isEmpty()) throw new EmptyStackException(); E element = elements.pop(); if (mins.peek().compareTo(element) == 0) mins.pop(); return element; } public E min() { if (mins.isEmpty()) throw new EmptyStackException(); return mins.peek(); } }


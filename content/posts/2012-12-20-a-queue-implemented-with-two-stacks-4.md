+++
date = "2012-12-20T04:39:00+00:00"
draft = false
tags = ["computation", "code"]
title = "A queue implemented with two stacks"
+++
Java:

    public class DoubleStackQueue<E>
    {
    	private Stack<E> enqueue = new Stack<E>(), dequeue = new Stack<E>();

    	private void move(Stack<E> from, Stack<E> to)
    	{
    		while (!from.empty()) to.push(from.pop());
    	}

    	public void enqueue(E element)
    	{
    		if (!dequeue.empty()) move(dequeue, enqueue);
    		enqueue.push(element);
    	}

    	public E dequeue()
    	{
    		if (isEmpty()) throw new IllegalStateException();
    		if (!enqueue.empty()) move(enqueue, dequeue);
    		return dequeue.pop();
    	}

    	public boolean isEmpty()
    	{
    		return enqueue.empty() && dequeue.empty();
    	}
    }

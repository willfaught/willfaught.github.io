+++
date = "2012-12-27T07:49:00+00:00"
draft = false
tags = ["computation", "code"]
title = "A stack implemented with two queues"
+++
Java:

    public class DoubleQueueStack<E>
    {
        private LinkedList<E> store = new LinkedList<E>(), other = new LinkedList<E>();
    
        public void push(E e)
        {
            store.offer(e);
        }
    
        public E pop()
        {
            if (store.isEmpty()) throw new IllegalStateException();
            E e = null;
            while (!store.isEmpty())
            {
                e = store.remove();
                if (store.isEmpty())
                {
                    break;
                }
                other.offer(e);
            }
            LinkedList<E> t = store;
            store = other;
            other = t;
            return e;
        }
    
        public boolean isEmpty()
        {
            return store.isEmpty();
        }
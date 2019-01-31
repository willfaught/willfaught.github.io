+++
date = "2012-12-23T06:18:00+00:00"
draft = false
tags = ["computation", "code"]
title = "Reorder an array randomly"
+++
Java:

    static <T> void shuffle(T[] a)
    {
        Random r = new Random();
        for (int i = a.length; i > 1; --i)
        {
            int j = r.nextInt(i);
            T t = a[i - 1];
            a[i - 1] = a[j];
            a[j] = t;
        }
    }

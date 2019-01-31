+++
date = "2013-03-20T01:25:01+00:00"
draft = false
tags = ["computation", "code"]
title = "Parse a signed integer"
+++
Java:

    int atoi(String s)
    {
        if (s == null) throw new NullPointerException();
        int length = s.length();
        if (length == 0) throw new NumberFormatException();
        char[] c = s.toCharArray();
        int n = 0;
        boolean negative = c[0] == '-';
        for (int i = negative ? 1 : 0; i < length; ++i)
        {
            if (!Character.isDigit(c[i]))
                throw new NumberFormatException();
            n = n * 10 + (c[i] - '0');
        }
        return negative ? -n : n;
    }

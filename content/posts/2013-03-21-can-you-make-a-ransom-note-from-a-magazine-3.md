+++
date = "2013-03-21T01:59:17+00:00"
draft = false
tags = ["computation", "code"]
title = "Can you make a ransom note from a magazine?"
+++
Java:

    boolean ransom(String note, String magazine)
    {
        if (note == null || magazine == null)
            throw new NullPointerException();
        if (note.length() == 0) return true;
        if (magazine.length() == 0
            || magazine.length() < note.length()) return false;
        char[] cs = note.toCharArray();
        Map<Character, Integer> m = new HashMap<>();
        for (char c : cs)
            if (m.containsKey(c)) m.put(c, m.get(c) + 1);
            else m.put(c, 1);
        cs = magazine.toCharArray();
        for (char c : cs)
            if (m.containsKey(c))
            {
                int i = m.get(c);
                if (i == 1)
                {
                    m.remove(c);
                    if (m.isEmpty())
                    {
                        break;
                    }
                }
                else m.put(c, i - 1);
            }
        return m.isEmpty();
    }
---
date: '2013-03-20T19:36:23-07:00'
tags:
- computation
- code
title: Find a substring
---

Java: boolean contains(String string, String substring) { if (string == null || substring == null) throw new NullPointerException(); int sublength = substring.length(); if (sublength == 0) return true; int length = string.length(); if (length < sublength) return false; char[] cs = string.toCharArray(); char[] subcs = substring.toCharArray(); for (int i = 0; i < length; ++i) { if (cs[i] == subcs[0]) { int j = 1; while (j < sublength && cs[i + j] == subcs[j]) ++j; if (j == sublength) return true; } } return false; }

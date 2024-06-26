+++
date = 2015-05-26T19:40:56-07:00
link = "https://security.stackexchange.com/questions/83660/simple-string-comparisons-not-secure-against-timing-attacks"
tags = ["coding"]
title = "Simple String Comparisons Not Secure Against Timing Attacks"
+++

Stack Exchange user cpast explains the security flaw in doing simple string comparisons for passwords and other sensitive information that enables timing attacks:

>The problem here is that generic string comparison functions return as soon as they find a difference between the strings. If the first byte is different, they return after just looking at one byte of the two strings. If the only difference is in the last byte, they process both entire strings before returning. This speeds things up in general, which is normally good. But it also means someone who can tell how long it takes to compare the strings can make a good guess where the first difference is.

Use a constant-time comparison instead where all characters are always compared.

+++
categories = ["technology"]
date = 2023-10-02T22:49:44-07:00
description = "How do you encode politically-contentious country flags?"
id = "d8be47dead27406b8be4a5a640f62d5a2f81a945"
link = "https://esham.io/2014/06/unicode-flags"
tags = ["unicode"]
title = "How Unicode Country Flags Work"
+++

Benjamin Esham:

>Version 6.0 of the Unicode standard, released in October 2010, added support for emoji. Aside from the classics like 😃 (SMILING FACE WITH OPEN MOUTH), 👍 (THUMBS UP SIGN), and 💩 (PILE OF POO), the standard also included several national flags like these:
>
>🇺🇸 🇩🇪 🇬🇧 🇯🇵 🇮🇹
>
>In fact, the standard included every national flag, and in a way that won't require the standard to be changed when new countries come into being. How did the Unicode Consortium pull this off?
>
>What they did is both crazy and genius. Instead of assigning a codepoint to each flag, which is the obvious way to do it (and the way the rest of the emoji are encoded), the standard defines twenty-six “regional indicator symbols”, from U+1F1E6 REGIONAL INDICATOR SYMBOL LETTER A to U+1F1FF REGIONAL INDICATOR SYMBOL LETTER Z. In order to include a country's flag in your text, you first look up the country's two-letter ISO 3166-1 code and then write the two regional indicator symbols corresponding to those letters. A font with support for that flag treats the two-codepoint sequence as a ligature, replacing the combination with a single pictogram.

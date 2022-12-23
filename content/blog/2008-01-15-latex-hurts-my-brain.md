---
categories:
- life
date: '2008-01-15T16:36:00-08:00'
tags:
- bad
- documents
- latex
- reflections
- wysiwyg
title: LaTeX HuRtS My BrAiN
---

[LaTeX](https://en.wikipedia.org/wiki/LaTeX) is a tool used almost exclusively for formatting academic Computer Science papers. Instead of formatting your paper through traditional [what-you-see-is-what-you-get](https://en.wikipedia.org/wiki/WYSIWYG) (WYSIWYG) means, LaTeX is a [markup language](https://en.wikipedia.org/wiki/Markup_language) that is interpreted and formatted for you. For example, instead of highlighting a word and clicking a bold button to make the font bold, e.g. **bold**, you embed a markup notation that is transformed into the intended formatting at the end, e.g. \textbf{bold}. The argument goes that this allows you to focus more on the content of your paper and not have to screw around with fonts and spacing all the way through the writing process.

What I can't figure out is why it's such a giant clusterfuck when it comes to actually getting anything to work with it. WYSIWYG may have its shortcomings, *but at least it works out of the box*. There's been something wrong with every LaTeX distribution I've ever tried. You'd think a tool used by Computer Scientists all the time would have been improved by now. I don't understand why academics aren't up in arms. Maybe one person got it working, and then everyone else just copied his or her setup. That reminds me of a joke I heard about how there isn't really any Makefile format specification, just a master Makefile floating around from which all examples are copied.

Here are the commands to format your LaTeX document:

1. latex document.tex
2. bibtex document.tex
3. latex document.tex
4. latex document.tex

That's right, you have to invoke latex *three times*. Why? I guess twice wasn't good enough.

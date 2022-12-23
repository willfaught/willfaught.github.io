---
categories:
- life
date: '2008-06-17T16:54:00-07:00'
tags:
- lisp
- reflections
- universal machine
title: My Lisp Enlightenment
---

From [Structure And Interpretation Of Computer Programs](https://mitpress.mit.edu/sicp/full-text/book/book-Z-H-4.html#%_toc_%_sec_4.1.5):

>In a similar way, we can regard the evaluator as a very special machine that takes as input a description of a machine. Given this input, the evaluator configures itself to emulate the machine described. For example, if we feed our evaluator the definition of factorial, the evaluator will be able to compute factorials.
>
>From this perspective, our evaluator is seen to be a universal machine. It mimics other machines when these are described as Lisp programs. This is striking. Try to imagine an analogous evaluator for electrical circuits. This would be a circuit that takes as input a signal encoding the plans for some other circuit, such as a filter. Given this input, the circuit evaluator would then behave like a filter with the same description. Such a universal electrical circuit is almost unimaginably complex. It is remarkable that the program evaluator is a rather simple program.

I see the light! (It's full of stars!)

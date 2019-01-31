+++
date = "2011-02-04T16:00:07+00:00"
draft = false
tags = ["sicp", "lisp", "universal machine"]
title = "Quote post"
+++
<p><a href="http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-26.html#%_sec_4.1.5">Structure and Interpretation of Computer Programs</a></p> 

> In a similar way, we can regard the evaluator as a very special machine that takes as input a description of a machine. Given this input, the evaluator configures itself to emulate the machine described... From this perspective, our evaluator is seen to be a universal machine. It mimics other machines when these are described as Lisp programs. This is striking. Try to imagine an analogous evaluator for electrical circuits. This would be a circuit that takes as input a signal encoding the plans for some other circuit, such as a filter. Given this input, the circuit evaluator would then behave like a filter with the same description. Such a universal electrical circuit is almost unimaginably complex. It is remarkable that the program evaluator is a rather simple program.
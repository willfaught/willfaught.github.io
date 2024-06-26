+++
date = 2011-05-31T23:00:06-07:00
link = "https://www.thc.org/root/phun/unmaintain.html"
tags = ["computation", "funny"]
title = "How To Write Unmaintainable Code"
+++

Insidious and hilarious stuff:

>Randomly capitalize the first letter of a syllable in the middle of a word. For example ComputeRasterHistoGram().

>Wherever the rules of the language permit, give classes, constructors, methods, member variables, parameters and local variables the same names. For extra points, reuse local variable names inside {} blocks. The goal is to force the maintenance programmer to carefully examine the scope of every instance. In particular, in Java, make ordinary methods masquerade as constructors.

>Choose variable names that masquerade as mathematical operators, e.g.:
>openParen = (slash + asterix) / equals;

>Choose variable names with irrelevant emotional connotation. e.g.:
>marypoppins = (superman + starship) / god;
>This confuses the reader because they have difficulty disassociating the emotional connotations of the words from the logic they're trying to think about.

>Ignore the Sun Java Coding Conventions, after all, Sun does. Fortunately, the compiler won't tattle when you violate them. The goal is to come up with names that differ subtlely only in case. If you are forced to use the capitalisation conventions, you can still subvert wherever the choice is ambigous, e.g. use both inputFilename and inputfileName. Invent your own hopelessly complex naming conventions, then berate everyone else for not following them.

>Use lower case l to indicate long constants. e.g. 10l is more likely to be mistaken for 101 that 10L is.

>Declare a global array in module A, and a private one of the same name in the header file for module B, so that it appears that it's the global array you are using in module B, but it isn't. Make no reference in the comments to this duplication.

>Hungarian Notation is the tactical nuclear weapon of source code obfuscation techniques; use it! Due to the sheer volume of source code contaminated by this idiom nothing can kill a maintenance engineer faster than a well planned Hungarian Notation attack.

>Compiler directives were designed with the express purpose of making the same code behave completely differently. Turn the boolean short-circuiting directive on and off repeatedly and vigourously

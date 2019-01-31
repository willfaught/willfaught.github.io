+++
date = "2011-07-17T05:55:17+00:00"
draft = false
tags = ["computation", "opinions"]
title = "Learning Ruby"
+++
I've been learning some Ruby the past week or so. Some thoughts:

* Arrays seem to be lists.
* Ranges are needed because of eager evaluation.
* I like that anything defined can be changed later, even visibility or constants.
* I haven't found a good explanation of blocks yet, but they appear to be first-class functions with some kind of iterator functionality grafted on.
* No homoiconic syntax.
* Abbreviated keywords (def). Yuck.
* No exposed class variables is a great idea. Methods only!
* Attribute generators are great for saving time, but is this done through some kind of extensible macro system?
* The + operator seems to do several things, depending on the operands' types. Is this extensible, or baked in?
+++
date = 2013-05-13T23:35:37-07:00
tags = ["computation"]
title = "Exit Status-Colored Bash Prompt"
+++

My `PS1` and `PS2` Bash prompts: function colorprompt { status="$?" bold="\\[\033[1m\\]" cyan="\\[\033[1;36m\\]" red="\\[\e[0;31m\\]" off="\\[\033[m\\]" if [ "$status" -eq 0 ] then export PS1="$bold$cyan\$$off " else export PS1="$bold$red\$$off " fi export PS2="$bold>$off " } export PROMPT_COMMAND=colorprompt The color of the prompt is cyan if the last exit status was zero, otherwise it's red. I disliked having little space to type commands in deeply nested directories, and I'm usually the same user on the same machine, so I only have the prompt `$` terminator.

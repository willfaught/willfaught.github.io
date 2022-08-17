---
date: '2014-11-29'
tags:
- computing
title: Bash prompt
---

I really like the Bash prompt I use now. It's just a `$` followed by a space. The `$` is green if `$?` is zero and red otherwise. If the current working directory is in a Git repository, the name of the checked-out branch appears before the `$` in blue with a space in between. Red, green, blue. Simple and elegant. It's great. function customprompt { EXITSTATUS="$?" BOLD="\\[\033[1m\\]" RED="\\[\033[1;31m\\]" GREEN="\\[\033[1;32m\\]" BLUE="\\[\033[1;34m\\]" OFF="\\[\033[m\\]" BRANCH=$(__git_ps1 | tr -d '( )') if [ ! -z $BRANCH ] then BRANCH="$BRANCH " fi if [ "${EXITSTATUS}" -eq 0 ] then PS1="$BOLD$BLUE$BRANCH$GREEN\$$OFF " else PS1="$BOLD$BLUE$BRANCH$RED\$$OFF " fi PS2="${BOLD}>${OFF} " } export GIT_PS1_SHOWDIRTYSTATE=1 export PROMPT_COMMAND=customprompt

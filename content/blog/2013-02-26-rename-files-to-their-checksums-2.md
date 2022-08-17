---
date: "2013-02-26T04:34:00+00:00"
draft: false
tags: ["computation", "code"]
title: "Rename files to their checksums"
---
Bash: #!/bin/bash if [ -z "$1" ] then echo "renamehash: must specify a source directory" exit 1 fi if [ -z "$2" ] then echo "renamehash: must specify a target directory" exit 1 fi fromdir="$1" todir="$2" if [ ! -d "$fromdir" ] then echo "renamehash: path $1 is not a directory" exit 1 fi if [ -e "$todir" ] then echo "renamehash: path $1 already exists" exit 1 fi mkdir -p "$todir" find "$fromdir" -type f -not -name .DS_Store | while read f do oldpath="$f" file=`basename "$oldpath"` extension="${file##*.}" oldsum=`sha1sum -b "$oldpath" | cut -d " " -f 1` newpath="$todir/$oldsum" if [[ "$file" == *"."* ]] then newpath="$newpath.$extension" fi if [ ! -e "$newpath" ] then ln "$oldpath" "$newpath" fi done You must have `sha1sum` installed. It operates on whole directories. It ignores .DS_Store because I use a Mac. It makes hard links in a new directory to keep things simple.

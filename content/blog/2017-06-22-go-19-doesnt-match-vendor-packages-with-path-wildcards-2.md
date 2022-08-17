---
date: "2017-06-22T10:12:29+00:00"
draft: false
tags: ["go", "golang", "1.9"]
title: "Go 1.9 doesn't match vendor packages with path wildcards"
---
From https://tip.golang.org/doc/go1.9:

>By popular request, ./... no longer matches packages in vendor directories in tools accepting package names, such as go test. To match vendor directories, write ./vendor/.... Finally! This removes the need for any kind of vendor-aware Makefile. I was worried when the suggestion of adding a -vendor flag was rejected.

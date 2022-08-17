---
date: '2015-02-25'
link: https://help.backblaze.com/entries/23311183-Why-do-my-backed-up-files-appear-smaller-on-Backblaze-than-on-my-Mac-
tags: null
title: Why do my backed up files appear smaller on Backblaze than on my Mac?
---

Backblaze on how OS X displays file sizes:

>Starting with Mac OS X 10.6, Apple calculates disk storage using decimal (base 10) math:
>
>- 1 KB is 1,000 bytes
>- 1 MB is 1,000,000 bytes or 1,000 KB
>- 1 GB is 1,000,000,000 bytes or 1,000 MB
>
>Backblaze's restore browser, Windows, and Mac OS X 10.5 and earlier all use binary (base 2) math:
>
>- 1 KB is 1,024 bytes
>- 1 MB is 1,048,576 bytes or 1,024 KB
>- 1 GB is 1,073,741,824 bytes or 1,024 MB

Why on Earth would they use base 10?

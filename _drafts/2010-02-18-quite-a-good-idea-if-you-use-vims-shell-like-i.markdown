---
layout: post
title: vim subshell indicator in PS1
date: '2010-02-18T21:17:38+01:00'
tags:
- shell
- vim
- ps1
tumblr_url: http://saji-codes.tumblr.com/post/397067498
---

Quite a good idea, if you use vim’s :shell, like I do, is to put this to your .bashrc:

```sh
test -n "$VIMRUNTIME" && PS1="\e[30m(vim shell)\e[0m $PS1"
```

Without this I often ended up forgetting that I’m in vim’s subshell and trying to open files that I’ve had already opened.

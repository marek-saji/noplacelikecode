---
layout: post
title: git branch in PS1
date: '2010-05-09T21:59:00+02:00'
tags:
- shell
- ps1
tumblr_url: http://saji-codes.tumblr.com/post/584783770
---
Another nice addition to PS1.

When working on git repositories, it’s great to know what branch you are currently at:

```sh
PS1_git_branch() {
    remote="$( git remote -v | head -n1 | cut -f2 | cut -d\  -f1)"
    test "$remote" == "some remote you want to ignore" && return
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1) /'
} 
PS1="\$(PS1_git_branch)$PS1"
```


I have a git repository in my home directory, so that I can easily sync some config files between all my computers, so I am ignoring that remote. I wanted to ignore git repositories that have root in my home directory, but I did not find any fast way to determine that. Am I missing something?..



Another idea is to display branch, only when it’s not “master”:

```sh
PS1_git_branch() {
    remote="$( git remote -v | head -n1 | cut -f2 | cut -d\  -f1)"
    test "$remote" == "some remote you want to ignore" && return
    branch="$( git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1) /' )"
    test "$branch" != "(master) " && echo "$branch"
} 
PS1="\$(PS1_git_branch)$PS1"
```


original idea from github guides

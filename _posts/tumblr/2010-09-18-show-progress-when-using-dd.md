---
layout: post
title: show progress when using dd(1)
date: '2010-09-18T13:47:25+02:00'
tags: 
tumblr_url: http://saji-codes.tumblr.com/post/1142783813
---

dd(1) is a tool that you can use for, for example, backup the whole disk partition. Unfortunatelly it does not show progress, so you don’t really know how long will it take.



On the other hand, yes you can. Just add this to your .bashrc:

```sh
ddv ()
{ 
    dd "$@" & pid=$!
    printf "dd(1) forked, pid=%d\n\n" $pid
    while test -e /proc/$pid; do
        sleep 3
        kill -USR1 $pid
    done
}
```


Voila, this will send dd process in the background and send a USR1 signal to it every 3s, which makes it display the progress.

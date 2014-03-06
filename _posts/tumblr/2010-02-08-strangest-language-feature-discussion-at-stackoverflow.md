---
layout: post
title: Strangest language feature discussion at StackOverflow
date: '2010-02-08T17:15:00+01:00'
tags:
- js
- c
- wtf
tumblr_url: http://saji-codes.tumblr.com/post/378257066
---
Strangest language feature discussion at StackOverflowI must admin that I read mainly C, JavaScript and PHP ones. My favourites:

C

a[10] == 10[a]
"Hello World"[i] == [i]"Hello World"
a[b][c] == b[c[a]]
"0123456789abcdef"[x & 0xf]

#define EVER ;;
for(EVER){}

// down to "operator"
for (x=20 ; x --> 0 ;){}

JavaScript

alert(''2'' + ''1.2'');     // 21.2 (concatenation)
alert(''2'' - - ''1.2'');   // 3.2 ("cast" to numeric and add)
alert(~~ -1.7);	        // -1 (removes metric part)
alert(typeof (+''1''));   // ''numeric'' (casting to "numeric")

.. and of course JavaScript truth table. {:

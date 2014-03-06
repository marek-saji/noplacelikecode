---
layout: post
title: echo is not different statement, than print
date: '2010-08-11T00:03:00+02:00'
tags:
- php
- wft
tumblr_url: http://saji-codes.tumblr.com/post/936442678
---
<?php
true && echo ''ok'';
fails with
Parse error: syntax error, unexpected T_ECHO in /home/saji/foo.php on line 2
but
<?php
true && print ''ok'';
works just fine

update
<?php
echo echo ''ok'';
also fails. It seems that things has changed in php-5.3 and echo does not return true anymore. Weird that print does, thought.

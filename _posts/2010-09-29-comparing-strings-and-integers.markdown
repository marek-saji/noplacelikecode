---
layout: post
title: Comparing strings and integers
date: '2010-09-29T10:43:00+02:00'
tags:
- php
tumblr_url: http://saji-codes.tumblr.com/post/1209529680
---
did you know?

When comparing int to string in php, string gets casted to integer, not the other way around.

```php
<?php
var_dump(42 == 'foo');   // false, (int)'foo'   === 0
var_dump(42 == 'foo42'); // false, (int)'foo42' === 0
var_dump(42 == '42foo'); // true,  (int)'42foo' === 42
```

encoutered by bartek.m

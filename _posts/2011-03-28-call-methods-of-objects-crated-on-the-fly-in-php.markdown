---
layout: post
title: call methods of objects crated on-the-fly in <abbr>PHP</abbr>
date: '2011-03-28T17:58:50+02:00'
tags:
- php
tumblr_url: http://saji-codes.tumblr.com/post/4160776672
---
PHP sux, that’s not news.

I always wanted to do this:

```php
(new Object())->method();
```

but it did not allow me. But there’s a neat trick, which makes it possible. Kind of. Just define small helper function:

```php
function o($o){return $o;}
```

and now:

```php
o(new Object())->method(); // almost like (new Object())->method();
```

voila!

You can also pretend to refer to array keys of arrays created on-the-fly.

```php
function k($a, $k){return $a[$k];}
```

```php
k(array_flip($array), 'key'); // almost like array_flip($array)['key']
```

### Update

Newer versions of <abbr>PHP</abbr> allows that syntax.

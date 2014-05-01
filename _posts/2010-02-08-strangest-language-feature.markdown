---
layout: post
title: Strangest language feature discussion at Stack­Overflow
date: '2010-02-08T17:15:00+01:00'
tags:
- javascript
- c
- wtf
tumblr_url: http://saji-codes.tumblr.com/post/378257066
link: http://stackoverflow.com/questions/1995113/strangest-language-feature/2001861
---
I must admin that I read mainly C, JavaScript and PHP ones. My favourites:

### C

```c
a[10] == 10[a]
"Hello World"[i] == [i]"Hello World"
a[b][c] == b[c[a]]
"0123456789abcdef"[x & 0xf]
```

```c
#define EVER ;;
for(EVER){}
```

```c
// down to "operator"
for (x=20 ; x --> 0 ;){}
```

### JavaScript

```js
alert('2' + '1.2');     // 21.2 (concatenation)
alert('2' - - '1.2');   // 3.2 ("cast" to numeric and add)
alert(~~ -1.7);	        // -1 (removes metric part)
alert(typeof (+'1'));   // 'numeric' (casting to "numeric")
```

… and of course [JavaScript truth table]. {:



[JavaScript truth table]: http://stackoverflow.com/a/1998224/146399

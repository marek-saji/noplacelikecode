---
layout: post
title: hightlighting code listings in posts
date: '2010-06-11T15:04:38+02:00'
tags:
- code
- syntax hightlight
- code hightlight
- code listings
tumblr_url: http://saji-codes.tumblr.com/post/686957505
---

This blag have some visitors apparently googling for a way to hightlight code listings on their tumblelogs. Just to make it easy for them I thought I’d write on what I’m using.


Google’s Prettify


It’s really straight-forward. Just include this JavaScript and CSS:


<script type="text/javascript" src="http://google-code-prettify.googlecode.com/svn/trunk/src/prettify.js"></script>
<link href="http://google-code-prettify.googlecode.com/svn/trunk/src/prettify.css" type="text/css" rel="stylesheet" />



and call prettyPrint(). You can bind it to onload or put this just before body ending tag:


(…)
<script type="text/javascript">prettyPrint();</script>
</body>
</html>


Now put your code listings in <pre class="prettyprint"><code>(…)</code><pre>. And you are set.


Supported syntaxes


Prettify supports all C-like (including JavaScript, PHP, Java etc.), bash-like and XML-like languages out-of-the box. If you want to use other languages you have to include additional JavaScript file for each one of them, e.g.:


<script type="text/javascript" src="http://google-code-prettify.googlecode.com/svn/trunk/src/lang-css.js"></script>


For list of all additional languages look inside prettify repository.



Prettify tries to auto-detect languages, but sometimes fails (e.g. two listings above brackets confused it), you can force some syntax by adding lang-{language code} (e.g. lang-c, lang-bsh, lang-html) as another class to pre.prettyprint.


Colours

You may not like default colour scheme (like me). Just play with the CSS. As for me, I’ve found among prettify’s issues template ported from vim’s desert theme and that’s what I’m using right now.

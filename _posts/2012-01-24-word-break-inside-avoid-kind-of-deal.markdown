---
layout: post
title: ! '`word-break-inside: avoid` kind of deal'
date: '2012-01-24T21:25:00+01:00'
tags:
- css
tumblr_url: http://saji-codes.tumblr.com/post/16420377169
---
I’ve spent some time looking for something like `word-break-inside: avoid` (sort of an opposite to `<wbr />` — a way to avoid breaking words in a inline element, but do that, if it’s longer than a line, just like page-break-inside: avoid does for pages in print). Turns out there’s none.

Just seemd that’s it’s too trivial thing to use JavaScritpt for that. But actually you can do just that using `display: inline-block`.

<iframe src="http://dabblet.com/gist/11193537" style="width:100%; height:10em">

```html
<p>
    Licensed under
    <span class="avoid-br">Creative Commons Attribute-ShareAlike 2.0</span>
</p>

<style>
  .avoid-br
  {
      display: inline-block;
  }
</style>
```

<a href="http://dabblet.com/gist/11193537">View the code and result.</a>

</iframe>

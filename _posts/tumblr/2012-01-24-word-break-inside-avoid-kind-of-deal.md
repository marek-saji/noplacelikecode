---
layout: post
title: ! '`word-break-inside: avoid` kind of deal.'
date: '2012-01-24T21:25:00+01:00'
tags:
- css
- css3
- word-break-inside
tumblr_url: http://saji-codes.tumblr.com/post/16420377169
category: css
---
I’ve spent some time looking for something like word-break-inside: avoid (sort of an opposite to <wbr /> — a way to avoid breaking words in a inline element, but do that, if it’s longer than a line, just like page-break-inside: avoid does for pages in print). Turns out there’s none.

Just seemd that’s it’s too trivial thing to use JavaScritpt for that. But actually you can do just that using display: inline-block.



<p>
    Licensed under
    <span class="avoid-br">Creative Commons Attribute-ShareAlike 2.0</span>
</p>

.avoid-br
{
    display: inline-block;
}

View the code and result.


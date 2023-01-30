---
title: 'Hyphenate headings as a last resort'
tweet: 'Hyphenate headings, but only as a last resort. #CSS #hyphens'
tweetUrl: 'https://twitter.com/saji_/status/1617236283440373762'
tootUrl: 'https://mastodon.social/@saji/109734413451924204'
tags:
- CSS
# https://codepen.io/marek-saji/full/WNKdrGe
---

<style>
  .demo div
  {
    font-size: 1.5em;
    width: 16ch;
    border: 2px dashed #aaa;
    resize: horizontal;
    overflow-x: scroll;
  }
</style>

Before I forgot about blogging for nine years in 2012, my
[last post here was about hyphenation][previously]. Surely things
progressed since then, right?

_Right?_

(Not really.)

## Need for hyphenation

Most of the time there is no need for hyphenation and text looks fine
aligned to the <del>left</del> start[^1]. Where hyphenation comes in
really handy is **headings**. It’s easy to end up with a heading set in
a relatively large size and somebody _will_ at some point put a
very–long–word™ there. Then browsers with narrow viewport will start
adding horizontal scrolling or -- <i>gasp!</i> -- your whole page will
be shrunk to accommodate the offending word.

Ok. Let’s start putting some long words in narrow containers and see what
happens.

<aside aria-hidden>
  <i>Note:</i>
  Demo boxes on this page are set to a width that aims to fit the word
  <q>extraordinarily</q>, but not <q>uncharacteristically</q>. If that
  renders differently for you — boxes are resizeable.
</aside>

By default words are not hyphenated and just stick out of the container.

<div
  id="demo-naked"
  class="demo"
  aria-hidden
>
  <div>
    Uncharacteristically and also extraordinarily long English words.
  </div>
</div>

## `hyphens: auto`

One would hope that adding [`hyphens: auto`][css-hyphens-auto] would
make things just work, but there are a few problems with it:

<div
  id="demo-hyphens-auto"
  class="demo"
  role="img"
  aria-label='Box with text "Uncharacteristically and also extraordinarily long English words.". Word "Uncharacteristically" is cut off. Word "extraordinarily" is hyphenated.'
>
  <style>
    #demo-hyphens-auto div
    {
      hyphens: auto;
    }
  </style>
  <div>
    Uncharacteristically and also extraordinarily long English words.
  </div>
</div>

1. <q>Uncharacteristically</q> is not hyphenated. What gives?

   Turns out currently both Chrome and Firefox disable hyphenation of
   capitalised words[^2]. This is to avoid performing it on proper
   nouns. Good intention, but it significantly limits usage of
   hyphenation in
   headings, which usually starts with a capitalised word or even have
   all of them capitalised.

2. <q>extraordinarily</q> is short enough to fit in a line, but gets
   hyphenated.

   Browsers are eager to hyphenate. In our case we want to hyphenate
   words only as a last resort, when whole word doesn’t fit in a line --
   that’s currently not possible with CSS.

   We _could_ use [`hyphenate-limit-chars`][css-hyphenate-limit-chars]
   to make the situation better to some extend, but it’s not perfect and
   it’s only supported by Chrome.

3. Not visible in this example, but browser needs to support hyphenation
   for your language. Check out [`hyphens` browser compatibility table
   on MDN][mdn-hyphens-compat] -- support for non–English is spotty to
   seat the least (<span aria-label="bravo">👏 for</span> Firefox).

## Soft hyphens

You can instruct browsers of places where words can be broken using
`&shy;` HTML entity (or `U-00AD` Soft Hyphen character). This takes care
of capitalised words. As a bonus it also brings hyphenation to browsers
that don’t support `hyphens: auto` (at all or in your language).

Alas, hyphenation is still performed too eagerly. While we like that
<q>Uncharacteristically</q> is broken into two lines, since it’s longer
than our container, <q>extraordinarily</q> is not and could be carried
over to a next line.

<div
  id="demo-shy"
  class="demo"
  role="img"
  aria-label='Box with text "Uncharacteristically and also extraordinarily long English words.". Words "Uncharacteristically" and "extraordinarily" are hyphenated.'
>
  <div>
    Un&shy;cha&shy;ra&shy;cte&shy;ri&shy;sti&shy;ca&shy;lly and also
    ex&shy;tra&shy;or&shy;di&shy;na&shy;ri&shy;ly long Eng&shy;lish
    words.
  </div>
</div>

## The solution

Let’s try to address the shortcomings of previous attempts with what we
have at our disposal in 2023.

You will not like it.

In addition to adding soft hyphens, wrap _each word_ in an element with
`display: inline-block`.

This will prefer carrying whole block to the next line before
hyphenating. Effectively only breaking the word as a last resort -- when
it doesn’t fit in the line on it’s own.

<div
  id="demo-final"
  class="demo"
  role="img"
  aria-label='Box with text "Uncharacteristically and also extraordinarily long English words.". Word "Uncharacteristically" is hyphenated, but "extraordinarily" is not.'
>
  <style>
    #demo-final .word
    {
      display: inline-block;
    }
  </style>
  <div>
    <span class="word">Un&shy;cha&shy;ra&shy;cte&shy;ri&shy;sti&shy;ca&shy;lly</span>
    <span class="word">and</span>
    <span class="word">also</span>
    <span class="word">ex&shy;tra&shy;or&shy;di&shy;na&shy;ri&shy;ly</span>
    <span class="word">long</span>
    <span class="word">Eng&shy;lish</span>
    <span class="word">words.</span>
  </div>
</div>

Here’s a JavaScript snippet to process your headings (you are a
responsible adult and will do it only on server side, right?):

```js
import { hyphenateSync } from 'hyphens/en';

hyphenateSync(text.trim())
  .split(/\s+/)
  .map(word => `<span class=word>${word}</span>`)
  .join(' ');
```

Note that this will increase weight of your HTML, so probably don’t run
it everywhere. I’d only do it on headings and other elements that will be
displayed in a bigger font.

This solution definitely
has a distinct ’90s smell all over it and makes you feel dirty
when implementing it, but it does work.

## The future

There is a [discussion on CSS WG about allowing hyphenating capitalised
words][csswg-control]. With this and
[`hyphenate-limit-chars`][css-hyphenate-limit-chars] we’ll hopefully get
a reasonable CSS–only solution in the future.


[^1]: Read about [logical properties](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Logical_Properties).
[^2]: [Initial discussion that lead to disabling hyphenation for capitalised words in CSS spec](https://github.com/w3c/csswg-drafts/issues/3927).

[previously]: {% post_url 2012-01-24-word-break-inside-avoid-kind-of-deal %}
[css-hyphens-auto]: https://w3c.github.io/csswg-drafts/css-text/#valdef-hyphens-auto
[css-hyphenate-limit-chars]: https://w3c.github.io/csswg-drafts/css-text-4/#propdef-hyphenate-limit-chars
[mdn-hyphens-compat]: https://developer.mozilla.org/en-US/docs/Web/CSS/hyphens#browser_compatibility
[npm-hyphen]: https://www.npmjs.com/package/hyphen
[csswg-control]: https://github.com/w3c/csswg-drafts/issues/5157

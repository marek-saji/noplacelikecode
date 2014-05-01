---
layout: post
title: Force HTTPS conn­ection for allegro.pl
date: '2010-12-02T23:06:00+01:00'
tags:
- security
tumblr_url: http://saji-codes.tumblr.com/post/2074488121
---
<ins class=block>
  Update:
  this recipe is deprecated; allegro does not allow to view all pages via <tt>https://ssl.allegro.pl</tt> anymore and just redirects to plain http.
</ins>

With starting to use WiFi on my new laptop I stareted to be a little paranoid.
I was forcing https connections for some sites with
[NoScript].
While it works great for sites that has http and https set on
the same domain (google.com, facebook.com, twitter.com),
it did not for allegro.pl (polish auctions portal).

Allegro uses <tt>https://ssl.allegro.pl/</tt> for signing-in,
sets non-secure cookie there and redirects to <tt>http://allegro.pl/</tt>
allowing session hijacking. But whole content is available
via <tt>https://ssl.allegro.pl/</tt>.

At first I tried to use
<q>[Force encryption for all the cookies set over HTTPS..][NoScriptQ6.4]</q>
feature of NoScript, but it did not work — after logging in via https
(and setting some secure cookies),
I was logged in in http as well.

Maybe allegro.pl is setting non-secure cookies on http basing on <tt>GET</tt> attributes?
I really don’t want to know.
I just had to make sure that I never, ever visit allegro.pl via http.

### The recepie

You will need root access to computer you are using.

#### Kill the original allegro.pl

We don’t want to reach allegro.pl. Ever.
So why not just redirect the trafic to localhost?
Add this to your `/etc/hosts`:

```
127.0.0.1    allegro.pl www.allegro.pl
```

#### Redirect traffic to ssl.allegro.pl

While we grabbed the traffic we might as well redirect it to ssl host.
Create new virtual host in apache:

```apache
<VirtualHost *:80>
    ServerName "allegro.pl"
    ServerAlias "www.allegro.pl"

    RewriteEngline On
    # prevent redirect loop after signing-in
    RewriteRule ^/direct_login.php    https://ssl.allegro.pl/myaccount/ [L,R,NE]
    RewriteRule ^/?(.*)               https://ssl.allegro.pl/$1 [L,R,NE]
</VirtualHost>
```

And voilà.
You can modify it to use with other sites as well.
A pity that <tt>delicious.com</tt> does not serve
all it’s content via some https…

And <tt>tumblr.com</tt> for that matter.



[NoScript]: http://noscript.net/
[NoScriptQ6.4]: http://noscript.net/faq#qa6_4

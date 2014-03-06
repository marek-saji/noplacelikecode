---
layout: post
title: Force HTTPS connection for allegro.pl
date: '2010-12-02T23:06:00+01:00'
tags:
- https
- ssl
- allegro
- apache
- security
tumblr_url: http://saji-codes.tumblr.com/post/2074488121
---
NOTE: this recipe is deprecated; allegro does not allow to view all pages via https://ssl.allegro.pl anymore and just redirects to plain http.

With starting to use WiFi on my new laptop I stareted to be a little paranoid.
I was forcing https connections for some sites with
NoScript.
While it works great for sites that has http and https set on
the same domain (google.com,
facebook.com,
twitter.com),
it did not for allegro.pl (polish auctions portal).

Allegro uses https://ssl.allegro.pl/ for signing-in,
sets non-secure cookie there and redirects to http://allegro.pl/
allowing session hijacking. But whole content is available
via https://ssl.allegro.pl/.

At first I tried to use
"Force encryption for all the cookies set over HTTPS.."
feature of NoScript, but it did not work — after logging in via https
(and setting some secure cookies),
I was logged in in http as well.

Maybe allegro.pl is setting non-secure cookies on http basing on GET attributes?
I really don’t want to know.
I just had to make sure that I never, ever visit allegro.pl via http.

The recepie

You will need root access to computer you are using.

Kill the original allegro.pl

We don’t want to reach allegro.pl. Ever.
   So why not just redirect the trafic to localhost?
   Add this to your /etc/hosts:

127.0.0.1    allegro.pl www.allegro.pl

Redirect traffic to ssl.allegro.pl

While we grabbed the traffic we might as well redirect it to ssl host.
   Create new virtual host in apache:

<VirtualHost *:80>
    ServerName "allegro.pl"
    ServerAlias "www.allegro.pl"

    RewriteEngline On
    # prevent redirect loop after signing-in
    RewriteRule ^/direct_login.php    https://ssl.allegro.pl/myaccount/ [L,R,NE]
    RewriteRule ^/?(.*)               https://ssl.allegro.pl/$1 [L,R,NE]
</VirtualHost>

And voilà.
You can modify it to use with other sites as well.
A pity that delicious.com does not serve
all it’s content via some https..

And tumblr.com for that matter.

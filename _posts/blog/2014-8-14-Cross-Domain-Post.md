---
layout: post
title: Cross domain post.
---
The post is about how to process Cross Domain POST.

As may know, JSONP is design to fixed cross domain GET, so what about POST ? After a GOOGLE or BAIDU, you may get your answer.

What I want to say is just as it describe.
I don't want to deplicate the answer more, just copy some code here:

![cross site post code snippt](/images/2014_08/cross_site_post_code.png)

The code works well in website with HTTP protocol, but no HTTPS.

Accessing an resource from HTTP site outside of an HTTPS site is not allowed.

Sometime you may resolved this by changing the configuration of CROSS-DOMAIN-ALLOW-ORIGIN, which I haved give a try. 

My backend colleague offer me some advice on add some proxy rule in Nginx.




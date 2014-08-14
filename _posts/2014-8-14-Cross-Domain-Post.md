---
layout: post
title: Cross domain post.
---
The post is about how to process Cross Domain POST.

As may know, JSONP is design to fixed cross domain GET, so what about POST ? After a GOOGLE or BAIDU, you may get your answer.

What I want to say is just as it describe.
I don't want to deplicate the answer more, just copy some code here:


```html
 <form target="remote" action="http://example.com/your_target_url?callback=YOUR_CALLBACK" method="POST">
     <input name="key1" value="val1" type="hidden"/>
     <input name="key2" value="val2" type="text"/>
 </form>
 <iframe name="remote" height="0" width="0" border="0"></iframe>
 <script>
     //set the domain
     window.domain = example.com;

     //bind the callback
     window.YOUR_CALLBACK=function(result){
         //process cross domain post result here    
     };
 </script>
```

The code works well in website with HTTP protocol, but no HTTPS.

Accessing an resource from HTTP site outside of an HTTPS site is not allowed.

Sometime you may resolved this by changing the configuration of CROSS-DOMAIN-ALLOW-ORIGIN, which I haved give a try. 

My backend colleague offer me some advice on add some proxy rule in Nginx.




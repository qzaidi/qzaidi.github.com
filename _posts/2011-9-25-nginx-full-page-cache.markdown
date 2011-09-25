---
layout: post
style: text
title: Nginx Full page Caching for Magento
---

> Build a better mousetrap, and the world will beat a path to your door.
>
>													Ralph Waldo Emerson
>

Maybe, it will, or maybe it won't. But on web, if the world does beat a path to your door, don't make it wait (although www stands for *world wide wait*, let's not be literalists here). Jokes apart, you would want to be ready for the world when it beats a path to your door (aka website) - and if your website crawls to screeching, grinding halt, traffic would go elsewhere as fast as it came.  I spent some time recently looking at how to ensure this won't happen for a magento based e-commerce store, and this post is a result. Hope it comes handy for others trying to extract the best performance out of their existing web host, and be better prepared for that big traffic day.

But first, some background. Magento is a PHP based  shopping cart, that uses the familiar LAMP stack and the Zend Framework. Surprisingly, for a PHP app, it is very modular, but also very slow, as a result. While this post will specifically be about magento, the principles will apply to any web application, written in any language. 

There are a lot of things that you can do to to speed up a default installation of magento, e.g.

 * Set correct expires headers on static content - Browser Caching is your friend
 * Use Apache + fastcgi, instead of mod_php
 * Use Nginx + fastcgi
 * Put memcache between DB and PHP, and use memcache for session storage (takes a lot of load off mysql, if you were using mysql as your session backend)
 * Use a reverse proxy such as nginx, for serving static content (or use a CDN if you can afford that).
 * Use some PHP cache/accelerator, like APC

There are numerous articles on the net that describe how to do all of this, and each one of them would improve your ability to serve more pages per second, but only by so much. One technique that I found not so well described is using Full Page Caching.

Putting caching at  the right places is a tried and tested technique that helps you scale something even when it was not written to scale. Caching can be done at the data layer (memcache in front of db), by the db itself (mysql query cache), or at the presentation layer, where you can cache partial fragments or even Full Page. 

* * *
        
                  Browser Cache  			    FPC			   	 	    Code Caching	     MEMCACHE			 	
                  HTTP Request -----------------> WebServer ---------------> PHP Interpreter --------------------> Database 
        		     		                         									               QUERYCACHE
* * *

As expected, the closer your cache is to the user, the faster it will be. If you don't have the correct expires, cache-control, last-modified /etag headers, you are wasting a lot of your hosting resources. But assuming that you already took care of that - the next best thing would be Full Page Caching. I was able to serve 12K pages a second easily with apache bench on my macbook when using FPC - and I couldn't even come close to this with any other techniques (APC,memcache,fastcgi and so on).

If you are thinking this is great, let's go ahead and use a full page cache in front of our magento store, please wait a moment. In any dynamic website, you serve different content to different users, so there are limits to what you can cache. After all, if the content was static, why use PHP at all - nginx will do the job. The kind of full page caching I will be talking about will cache content only for new users, who have not yet added anything to their carts yet - but have just come to your site by clicking through an ad, or maybe from organic search. 

This isn't so much of a restriction as it might seem at first. For any magento shop that spends money on advertising, a significant portion of your users will be new. Unlike returning visitors, they will not have anything in their local browser cache, so your site probably performs worst for these. Depending on how much of cacheable static resources you have - your new vistors could be experiencing load times from 50-100% in excess of what your returning visitors will see. So it makes a lot of sense on spending effort on new visitors, and this frees up hosting resources for your returning visitors as well, so they would benefit too. 

In part 2 of this series, I will talk about the specifics of setting up an nginx full page cache in front of magento. 

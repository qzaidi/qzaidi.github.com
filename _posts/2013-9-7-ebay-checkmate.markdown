---
layout: post
style: text
title: Ebay Check Mate
---

I just noticed ebay advertising their [Chrome Extension](http://deals.ebay.in/chrome/index.html).


![Ebay Chrome Extension] (/img/ebaycheck.png)

And I was curious as to which sites they consider to be competitors.

Well, here is the list, hidden in manifest.json. 


- http://\*.futurebazaar.com/
- http://shopping.indiatimes.com/
- http://www.flipkart.com/
- http://www.indiaplaza.com/
- http://www.infibeam.com/
- http://www.amazon.in/
- http://www.amazon.com/
- http://www.naaptol.com/
- http://www.fashionandyou.com/
- http://www.snapdeal.com/
- http://www.tradus.com/
- http://www.yebhi.com/
- http://www.homeshop18.com/
- http://www.myntra.com/
- http://www.firstcry.com/
- http://www.junglee.com/
- http://www.jabong.com/
- http://\*.buyhatke.com/
- http://\*.mysmartprice.com/
- http://\*.91mobiles.com/
- http://\*.shopclues.com/
- http://\*.lenskart.com/
- http://\*.zovi.com/
- http://\*.shopyourworld.com/
- http://\*.univercell.in/
- http://www.themobilestore.in/
- http://www.cromaretail.com/
- http://mobiles.sulekha.com/
- http://www.healthgenie.in/
- http://www.hclstore.in/
- http://www.ezoneonline.in/
- http://www.reliancedigital.in/
- http://\*.gsmarena.com/
- http://\*.cnet.com/
- http://\*.wikipedia.org/
- http://\*.samsung.com/
- http://\*.nokia.com/

I say hidden, because while you have an option of skipping certain sites, its not shown which sites are covered.

![Ebay Check Options](/img/ebaycheckopts.png)

Congrats to everyone who has made the list, and better luck to everyone else still in the fray. Surely the order here doesn't matter, because as per comscore, the top 6 ecommerce websites are myntra, flipkart, jabong, amazon, snapdeal and homeshop18. [Source: Yahoo](http://in.finance.yahoo.com/photos/top-6-ecommerce-websites-in-india-slideshow/top-6-ecommerce-websites-in-india-photo-1374728087980.html)

Another interesting thing about this extension - it uses the product microdata to figure out the name and price. Yes, the same microdata that google requires you to insert in catalog pages for better search listings, makes ebay's job to crawl these disparate sites a wee bit easier.

I still have a few unanswered questions.

 _Why is wikipedia in the list of ecommerce sites and product review sites._
 That a significant number of people do product research on wikipedia seems counter-intuitive to me, and if they do, then by the same logic, why isn't google in that list? Only reason I can think of is that ebay is only scanning http content to steer clear of private content (or trouble). Most of the ecommerce site will have private pages like account and order history behind https, so skipping https helps them steer clear. And because google is only available on https, that is skipped here.

 _Price information is sent over to ebay, adjusted by a factor._
 Even if it wasn't - it would have been easy to figure that out from the REFERER header. But before being sent, its multiplied by 0.7. I won't believe that this is some poor attempt at obfuscating the price, simply because there are easier ways to get better obfuscation. Maybe the number 0.7 has some deeper meaning, which is beyond me at the moment. WDYT? 

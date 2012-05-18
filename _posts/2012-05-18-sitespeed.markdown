---
layout: post
style: text
title: Site Speed - How fast is fast enough
---

Its almost a cliche to say that if your site isn't fast enough, you are losing money on the table. At Urbantouch.com, we take this to heart though, and it was a design goal for us to build something that is blazingly fast, and that scales well. 

When running on the magento platform, one perennial complaint we used to hear was about the site being too slow. Slight increases in traffic would require increasing our EC2 Capacity, look for newer performance hacks like nginx micro-caching or client side javascript optimisations, and yet, at the end of it, we would have the same problem at hands. There is only so much you can do if you are starting with something like magento .For all its feature richness, magento trades off site speed and performance for flexibility and features.

Fast forward to April. On 5th of April, we launched our home grown e-commerce platform to visitors. On 19th of the same month, we switched our entire backend (Account Management, Order Management, Customer Management & Logistics) to the new platform. It has been a month since, the right time to reflect and see how it went. Here is what Alexa has to say.

![Urbantouch.com](/img/urbantouch.gif "Urbantouch.com site speed")

For a comparison, here is how our competitors do.

![Flipkart.com](/img/flipkart.gif "Flipkart.com site speed")
![Cleartrip.com](/img/cleartrip.gif "Cleartrip.com site speed")
![Infibeam.com](/img/infibeam.gif "Infibeam.com site speed")
![Zovi.com](/img/zovi.gif "Zovi.com site speed")
![Myntra.com](/img/myntra.gif "Myntra.com site speed")

For the sceptics who would say we have much less traffic compared to some of these sites - our traffic has increased 2-3X since we switched, and the new system is 10X more efficient (so we are using 1/10th of the resources we were using with magento), so we don't think this is going to change anything.

How did we do this? For one, instead of chosing the well trodden paths of java backend + php frontend, we chose node.js, and run on a javascript stack end to end (The async, event driven programming model does make a big difference, and sometimes praying to the Event Loop Gods actually helps). Second, we make a judicious use of caching on the client side. Third - well, we believe we have only scratched the surface, and there are quite a few more tricks down our sleeves, so watch out. 

If this sort of things interests you, we are still hiring - write to us at jobs at urbantouch dot com, to work on some of the most cutting edge stuff in web development and scalability.


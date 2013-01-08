---
layout: post
style: text
title: Shop with (out) Confidence
---
  
tl;dr - If you shop at Indiatimes Shopping, the world will know what you bought. Those of you facebookers who believe in social shopping and show off can stop reading right here.

Almost a year and a half ago, I wrote about Sosasta being compromised. 

Some of the best companies get caught with their pants down.  After all, there was that epic hacking of Mat Honan, where Customer Service proved to be the weak link in the security armor.

Which got me thinking. How do the India Ecommerce companies stack up today?

To be honest, no, that didn't get me thinking. I am pretty pissed off at Indiatimes shopping tonight. One of my orders was incorrectly fulfilled, and my complaint was closed without any update after about 10 days and 4 requests for updates. Staring at a Case log that looks something like this got me thinking.

![shopping.indiatimes.com](/img/complaint.png "What got me thinking?")

In the URL bar, you can see there is a case id. What if I change the case Id? Can I view other peoples cases. This is any crackers dream come true, where all it takes is incrementing a query param. Much has been written about new startups failing to put this validation in place, but Indiatimes shopping is no startup. Times of India Group is a heavyweight over here, and Indiatimes is probably the oldest ecommerce company in the country still around. So I tried, against hope, and surprise, surprise, for I was staring at someone else's case.

![shopping.indiatimes.com](/img/moneyrefunded.png "Give this guy a refund.")

Interesting. I can use this to estimate that there are roughly 2000 cases a day opened at IndiaTimes shopping. That in itself should speak for their legendary customer service.  However, other than the order id, and whatever details the user provided in the case (See in my case, I actually gave my phone number for a callback), there isn't much info to be found.

Or is it? A few minutes of playing around, and I can tell you given an order id, what items compromised that order.

![shopping.indiatimes.com](/img/orderitems.png "I know what you ordered last summer.")

Things just started to get interesting. It appears as if I can open a case on behalf of any party (read user). I am pretty sure another hour spent at this will get me more juice, from this apparently newly integrated case management system. But then, its now 2:00 AM in the midnight, and what is the point to be proven. Most of these websites pretty much show what they mean by Security via their login dialogues.

![shopping.indiatimes.com](/img/securelogin.png "It can't get more secure than this")

Over here, it says secure login. Your login details are secured by sending them over http, and by sending the password in plain text.

The more things change, the more they stay the same.

Happy Shopping :) The sale is on!

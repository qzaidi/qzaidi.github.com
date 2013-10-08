---
layout: post
style: text
title: How do you measure up?
tags: 
  - devops
---

>  You can't manage what you don't measure.
>

Following my previous [post on site speed](/2012/05/18/sitespeed/), I was asked about the 'hacks' we use to ensure that we are this fast. While there are several optimisation hacks and best practices that contribute to fast page load times without requiring you to spend a tonne on your hosting infra, the first question you need to ask is how to measure page load times. 

Measurement must precede any attempts at management, because premature optimisation is the root of all evil. And believe me, measuring site speed is a tricky thing. You can run wget/curl to measure download speeds, but HTML is only a fraction of page content, and you must measure how long it takes to render the complete page, down to the DOM Content Loaded event, and beyond.  So this post is about how you can measure site speed, and I will try to follow it up with more on managing site speed.

## Browser/Extension based measurements
Most browsers now come with a firebug like interface that gives you a nice waterfall representation of the page load times for each component on your page. In Chrome , Safari, Firefox and IE, look at the Network section of Developer Tools/ Firebug. Then there are extensions like YSlow and Page Speed Insights that analyze the information you see in network tab and give you an overall score and recommendations. When using this method, make sure you clear/disable cache to know the real time before the DOMContentLoaded event is fired.
The catch:  This is a manual process, and that very much depends on how representative your own internet connection is. Any site will load fast if the pipe is fat enough.

## Google Analytics

Google Analytics is very handy when it comes to measuring page load times. There are a couple of reports under Content->Site Speed with some really useful information. For example, you can examine individual page load times and also the breakdown. Site speed is not one metric after all. There is DNS lookup time, time to first byte (Avg. Server Response Time), Page Download time, DOM load time, and so on.

If you are depending on GA, here's a word of advice. While the numbers broadly look correct, Google's measurement is not very scientific or accurate over shorter periods of time. Its important to know that Google uses sampling, and a very small percentage of impressions are used, as only modern browsers supporting timing measurements are used for this. This makes it very unreliable for me. As an illustration, here's our page load speeds for homepage, on google's browser (chrome). Just look at how wildly this thing is varying.

![http://google.com/analytics](/img/ga-bug.png "Don't trust google")

Also, its important to understand the difference between first page load time and subsequent load times. Once the cache has been primed, the site should really load fast. If it won't, you need to relook at your caching strategy. Fortunately, Google allows you to do this - just view the load times by New User vs Returning measure to get an idea of how well you are using browser cache.

[Google Analytics](http://google.com/analytics)

## Pingdom

Pingdom has some excellent tools to offer, and their Load time testing tool is a good one, available for free. They retain a history of measurements automatically, so you can see how the site has been doing every time you do a manual test. The paid version allows for automatic measurements. The catch - free version only supports 2 locations, and when I last checked, the paid version had no locations from India. So if you are geographically restricted to a certain location, make sure your tool supports it.

[Pingdom](http://tools.pingdom.com/fpt/) 

## Webpagetest.org

Similar to pingdom, this one was originally developed by AOL, and is now open-sourced under a BSD license. You can run your own instance, or use the hosted one. Great thing about webpagetest - you can see load times by browser and location. And yes, Indian locations are available as well. Quite reliable, except for the fact that its a manual thing and you have to wait for your turn.

[Webpagetest](http://www.webpagetest.org/)

## Google Webmaster Tools/Alexa
Both Alexa and Google have a toolbar that they use to collect information on page load times, and if your site has decent traffic, this is an option. I have only used Alexa, and have found the toolbar approach more accurate than the sampling one, and I suppose the same may hold true of webmaster tools.

## GtMetrix
[Gtmetrix](http://gtmetrix.com) is something I discovered only recently, but if I have to recommend one tool out there that is the best, it is this. It offers you the recommendations/scores from Google Pagespeed and YSlow at one place, is way more usable, much faster than webpagetest.org, allows periodic monitoring on the free plan, and supports history/multiple locations/comparisons. Its free plan has features that beat some of the paid offerings in this space.

## Roll out your own
If you are really serious about fast page loading, I recommend that you use your own measurement scripts in addition to the above. If you have a local server, setup a script that measures how long it takes. Beyond using curl/wget, you can use headless browser testing tools to measure how long the site would load in a real browser. I would strongly recommend phantomjs(http://phantomjs.org). It comes with built in examples showing not only page load times, but also the full HAR. If you are wondering what HAR is, its an industry accepted standard (stands for HTTP Archive Specification) that shows you the full HTTP waterfall (all components on the page, including css, js and images), and is very helpful in understanding what is slowing you down. In the next episode of this post, I will focus on how you can use phantomjs and HAR, and maybe discuss a hack or two on how to speed things up without a lot of work.

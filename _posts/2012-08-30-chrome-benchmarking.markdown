---
layout: post
style: text
title: Benchmarking with chrome
---

I previously wrote about using [phantomjs to benchmark average page load times](/2012/07/05/phantomjs-require/). Well, it seems there is a way to do it lot more easily, with a chrome extension that is. The [Page Benchmarker](https://chrome.google.com/webstore/detail/channimfdomahekjcahlbpccbgaopjll) extension would not only average the page load times across multiple runs, but it would show you the standard deviation (something that I was only thinking of adding to my phantomjs script), and a host of other very useful numbers. All you need to do is to run chrome with the command line flag --enable-benchmarking.

Here's a screenshot of how things look like when I benchmarked urbantouch.com against google and fashionandyou.com. The google results looked so weird I had to do a rerun.

![https://chrome.google.com/webstore/detail/channimfdomahekjcahlbpccbgaopjll](/img/benchmark.png "Urbantouch.com site speed benchmarks")

I was unable to test the results with and without SPDY for urbantouch.com. This is because we have enabled SPDY only on the CDN (http://cdn1.urbantouch.com) , and the extension refuses to benchmark because it fails to find SPDY support on the main domain (i.e. www.urbantouch.com)

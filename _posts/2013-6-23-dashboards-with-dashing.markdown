---
layout: post
style: text
title: Real Time Dashboard using GA and dashing
---

My [last post](http://qzaidi.github.io/2013/06/21/autoscaling-with-aws/) finished with screengrab of our developer dashboard, and it generated a considerable interest, as evidenced by *dashing* repo trending to the top at [https://github.com/explore](github). 

Now dashing looks awesome on TV screens, but what good is a dashboard without real time data? As Yogi Berra said

> A dashboard is 90% data, and the other half is visualisation.

As an aside - I am not fabricating this. There is a quote from him that goes like this.

> I never said most of the things I said. -Yogi Berra

In my quest for cheap, ubiquitious, real time lies (er, stats), I turned to Google (Analytics). Plugging that data into dashify won't be hard, in theory.

> In theory there is no difference between theory and practice. In practice there is. 

I looked for the GA Real Time API and there was none, since forever. From the looks of it, no one should be expecting it either. It has been the [top requested feature on the analytics issue tracker](https://code.google.com/p/analytics-issues/issues/detail?id=154), circa fall 2011.

*Update - 8/2/2013*: It seems there will be an API. Google has now come up with an [invite only beta for the Real Time API](http://analytics.blogspot.co.uk/2013/08/google-analytics-launches-real-time-api.html), just about a month after writing this. I am not holding my breath though.

Can we work around that? I set out to seek an answer. After a few wasted attempts at Reverse Engineering and figuring how to generate SID and APISID and numerous other tokens, I gave up. And then I had some inspiration.

For the TV screen, we would anyway open the browser to show the dashboard.Why not open another tab, log in to GA, and then run a client side script that pipes this data to our custom dashboard. 

And it worked.

![https://github.com/qzaidi/dashing-js](/img/visitors.png "Visitors on the site")

Here's how. The technique is fairly generic, so even if you are using some other dashboard, [skip the steps specific to dashing](#galink) and you will still find salvation.

First, install dashing-js.  I use the [node.js port](https://github.com/qzaidi/dashing-js), modified to allow CORS requests. 

Install this from npm using

{% highlight bash %}
$ npm install -g git@qzaidi/dashing-js.git
{% endhighlight %}

Once installed, create a new project

{% highlight bash %}
$ dashing-js new mydashboard
{% endhighlight %}

This will create a directory named mydashboard. cd into it, and run npm install

{% highlight bash %}
$ cd mydashboard
$ npm install
{% endhighlight %}

All set to run the dashboard server. 

{% highlight bash %}
$ dashing-js start
{% endhighlight %}

Now head over to [http://localhost:3030/](http://localhost:3030/) to verify that everything is all right.

<a id='galink'>So far so good,  now let's get the Google Analytics Integration working.</a>

Open another browser tab, log into your GA account, and go to the Real Time -> Overview.

![https://www.google.com/analytics](/img/realtime.png "Google Analytics Real Time")

And then <a href="javascript: (function() { var script = document.createElement('script'); script.async = true; script.src = 'https://gist.github.com/qzaidi/6a15df8f3c2e5e61b8b0/raw/03d3b2f0915cfe05227e48a55354d45a2a086a62/galink'; document.getElementsByTagName('head')[0].appendChild(script)}())"> drag this link </a> to the GA Tab. 

This loads a script that will [observe the div element](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver) that contains the current visitor count, and send that value to the dashboard server via an XMLHTTPRequest. That's all, folks - by opening a seperate tab in the browser, and logging on to GA, you can fetch the data from GA, and visualize it in your dashing dashboard. There's hardly anything specific to dashing-js here, so you could really use this technique anywhere else.

<script type="text/javascript" src="https://gist.github.com/qzaidi/6a15df8f3c2e5e61b8b0.js">
</script>


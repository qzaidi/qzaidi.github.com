---
layout: post
style: text
title: The case for server side analytics
---

![https://google.com/analytics] (/img/googles-new-universal-analytics.jpg "Google Analytics")
There are few google services as widely used as Analytics, and in spite of its quirks, its great value for Money, better than a free lunch.

Almost, that is. While its so very easy to use GA scripts on the client side, we know

- you lose control over what is shared with Google.
- it slows down the page load. 
- if you have too much data, you outgrow the free plan, and the next tier blows a $150K hole in your pocket, once a year.

But with universal analytics, it becomes more attractive than ever, esp. for non-conventional use cases. By non-conventional, I mean [NY Times used twitter as their database.](http://open.blogs.nytimes.com/2011/06/15/using-twitter-as-your-database-2/) sort of thing.

Server Side Analytics has none of those drawbacks. 

- No slowing of page load times
- Complete control over what you share with google.
- Can be used with non js clients & devices.
- Sample and share events that are important, and stay within the limits of free plan.
- Real time insights - can be a zero cost replacement to services like new-relic
- Track CPU spikes, server exceptions, and what not

Before the [Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/v1/), server side GA was possible, but very hard, and prone to failures. Now, its a breeze.

Let me explain how we use Server side analytics in some interesting ways.

We need a way to track which links in our mailers are being clicked. Now we could use a link tracker, but there are features we needed
which most link trackers won't offer, and surely not for free.

For example, if goo.gl/i9qZuk redirects to mysite.com, we would want goo.gl/i9qZuk?q=123 to redirect to mysite.com/?q=123.
However, we will want link stats to be grouped on mysite.com (e.g. ignore the query).

While its very easy to write a redirect route that does this, it starts becoming complicated once you want to track

- total clicks to mysite.com via this link
- unique clicks
- unique clicks by day
- unique clicks by geo region

Our solution? Write the redirector ourselves, and use GA to track. Server side tracking allows us to send what we want to send to google (i.e. strip query string). And all sorts of slicing and dicing options that GA has are now available.

Advantages - we track what we want, and we share with Google only what we want to share. We can track things like CPU spikes, database timings, exceptions, and just about anything and everything we want to track, in near real time.

On to specifics. One could use the [universal-analytics](https://github.com/jtillmann/universal-analytics) npm module, and here's some sample code.

First, inside the app.js

{% highlight javascript %}
var ua = require('universal-analytics');
app.use(ua.middleware('UA-XXXXXXXXX-1'));
{% endhighlight %}

Then add a ga.js module
{% highlight javascript %}
var ua = require('universal-analytics');

var ga = {
  pageview: function(title) {
    return function(req,res,next) {
      var udata = {
        dp: req.path, 
        dt: title, 
        dh: 'https://mysite.com',
        uip: req.ip,
        ua: req.headers['user-agent']
      };
      if (req.visitor) { 
        req.visitor
           .pageview(udata)
           .send();
      }
      next();
    };
  }
};
module.exports = ga;
{% endhighlight %}

And finally, in the routes you want to track

{% highlight javascript %}
app.get('/mytracker',dosomething, donextthing, ga.pageview('Track Page'),render,handleError);
{% endhighlight %}

Even if you are not using node.js, here is a cool way to do this with nginx. Yes, I am talking of post\_action. 

[post\_action](http://wiki.nginx.org/HttpCoreModule#post_action) is the last phase in nginx processing of a request. Its often used for things like Download Tracking, and here we use it for visit tracking.

Here's a sample UA call.

{% highlight html %}
http://www.google-analytics.com/collect?dp=%2Fv1%2Fproducts%2F321312&dh=https%3A%2F%2Fcatalog.paytm.com&uip=8.8.8.8&dt=Testing%20Something&an=Android&av=4.1.2&v=1&tid=UA-36768858-3&cid=837e181c-4860-4728-8a9c-74e4c572e85c&t=pageview
{% endhighlight %}

Parameters like dp (path), dh, and uip are available fron nginx, so you could include a post\_action like this.

{% highlight bash %}
location / { 
  proxy_pass backend;
  post_action @log;
}
 
location @log {
  internal;
  proxy_pass http://www.google-analytics.com/collect?dp=$uri&dh=$host&tid=UA-Property&cid=$cookie_sessionid&uip=$http_x_forwarded_for&t=pageview
}

{% endhighlight %}





---
layout: post
style: text
title: Phantomjs, headless scripting and code reuse
tags: 
  - javascript
  - testing
---

[Phantomjs](http://phantomjs.org) is headless browser (Webkit based) with a javascript API. IMO, it is the best tool available if you are looking for any kind of Automated Browser Testing. But beyond that, you can use it for scraping, screenshotting, and even monitoring your own page load times. It comes with a comprehensive set of examples that demonstrate what is possible.

Phantomjs scripts look deceptively similar to node.js, but it is not node.js. When using it for testing, I missed the ability to create and require modules of my own. After all, regular phantom code looks like this.

{% highlight javascript%}
var page = require('webpage').create();
var system = require('system');
{% endhighlight %}

However, requiring a module of your own throws a weird error.

{% highlight bash%}
$phantomjs
phantomjs> var x = require('./test')
Unknown module ./test for require()
  phantomjs://bootstrap.js:53 in require
  ...
{% endhighlight %}

Well, the feature is there if you care to RTFM. It is called injectJs. You will see examples of injectJs being used with the page object, but this method also exists in phantom. So to really include test, you have to do this

{% highlight javascript%}
var page = require('webpage').create();
phantom.injectJs('./test.js');
{% endhighlight %}

Unlike node, there is no exports object, and every global in test.js is available. That shouldn't be any problem, because you can declare everything inside a function and then export what you need.

{% highlight javascript%}
var util = (function() {

  var privatevar;
  
  function privatefn() {
   ...
  }
 
    return {
      publicfn1: function() \{
      ...
    },

    publicfn2: function(msg) {

    }
  }; 

}());
{% endhighlight %}

This is fine, however, there is one flaw. I still miss the node.js require, which allows me to use a different name for util, should there be a conflict.

{% highlight javascript%}
var myutils = require('util');
{% endhighlight %}

It seems we can write a require and make things look more natural (read node like). You can create a require.js with the following content

{% highlight javascript%}
"use strict";

var phantom;
var exports;

var require = function(module) {
  phantom.injectJs(module);
  return exports;
};
{% endhighlight %}

And then, change your module to always use the name exports, instead of util, like this

{% highlight javascript%}

var exports = (function() {
  return {
    publicfn1: function() {
      ...
    },

    publicfn2: function() {
      ...
    }
  };
}());

{% endhighlight %}

And that's it. Now you can include require.js once using phantom.injectJs, and rest of the modules can then be imported using require.

How do I use phantomjs? For one, there are 2 builtin examples that are usable out of the box. 

{% highlight javascript %}
cd <Phantom Download Dir>/examples
phantomjs loadspeed.js http://www.urbantouch.com/
{% endhighlight %}

Now this is a very simple example that loads a webpage, timing the start and the end, and then it reports the overall load time. I have sligtly modified this script so you can do multiple measurements and run the whole thing in a loop. Here's the modified script.

<script src="https://gist.github.com/2993032.js"> </script>

{% highlight javascript%}
phantomjs loadloop.js http://www.urbantouch.com
{% endhighlight %}

The other one is netsniff.js, which lets us create an HAR, that can then be viewed using HTTPViewer on any web based [HAR viewer](http://softwareishard.com/har/viewer/). If this stuff interests you, I would also suggest checking out [confess.js](https://github.com/jamesgpearce/confess).

Beyond these examples, I have found it very useful to write regression tests that can run prior to every release. For an ecommerce website like ours, we have a test case to test the signup, add to cart , place order flow that looks like this.

{% highlight javascript%}

phantom.injectJs('./inc/require.js');

var util = require('util');

function signup(next) {
}

function addToCart(next) {
}

function checkout(next) {
}

function makePayment(next) {
}

function logout(next) {
}

util.router(signup,addToCart,checkout,makePayment,logout);
{% endhighlight %}


The router code is inspired by the router in [Express](http://expressjs.com), and allows us to avoid callback hell.

{% highlight javascript%}
var util = (function() {
  return { 
  router: function() {
  var routes = [].splice.call(arguments,0);

  (function pass(i) {
    function next() {
      pass(i+1);
    }

    if (routes[i])
      (routes[i])(next);
    else
      phantom.exit();
  })(0);
  }
}());
{% endhighlight %}

Running phantomjs tests on every deployment, we maintain our sanity and make sure the critical flows are still intact. This is necessary when you want to run real fast and do multiple deployments a day. Quality of your code is ensured by the quality of your regression tests.

This is it - a brief introduction to phantomjs that should get you started, and maybe as in my case, get you hooked.

---
layout: post
style: text
title: Phantomjs, headless scripting and code reuse
---

[Phantomjs](http://phantomjs.org) is headless browser (Webkit based) with a javascript API. IMO, it is the best tool available if you are looking for any kind of Automated Browser Testing. But beyond that, you can use it for scraping, screenshotting, and even monitoring your own page load times. It comes with a comprehensive set of examples that demonstrate what is possible.

Phantomjs scripts look deceptively similar to node.js, but it is not node.js. When using it for testing, I missed the ability to create and require modules of my own. After all, regular phantom code looks like this.

```
var page = require('webpage').create();
var system = require('system');
```

However, requiring a module of your own returns a weird error.

```
phantomjs> var x = require('./test')
Unknown module ./test for require()

  phantomjs://bootstrap.js:53 in require
  :1
  phantomjs://repl-input:1
```

Well, the feature is there if you care to RTFM. It is called injectJs. You will see examples of injectJs being used with the page object, but this method also exists in phantom. So to really include test, you have to do this

```
var page = require('webpage').create();


phantom.injectJs('./test.js');
```

Unlike node, there is no exports object, and every global in test.js is available. That shouldn't be any problem, because you can declare everything inside a function and then export what you need.

```
var util = (function() {

  var privatevar;
  
  function privatefn() {

  }
 
  return {
    publicfn1: function() {

    },

    publicfn2: function(msg) {

    }
  }; 

}());
```

How do I use phantomjs? For one, there are 2 builtin examples that are usable out of the box. 

```
cd <Phantom Download Dir>/examples
phantomjs loadspeed.js http://www.urbantouch.com/
```

Now this is a very simple example that loads a webpage, timing the start and the end, and then it reports the overall load time. I have sligtly modified this script so you can do multiple measurements and run the whole thing in a loop. Here's the modified script.

<script src="https://gist.github.com/2993032.js"> </script>

```
phantomjs loadloop.js http://www.urbantouch.com
```

The other one is netsniff.js, which lets us create an HAR, that can then be viewed using HTTPViewer on any web based [HAR viewer](http://softwareishard.com/har/viewer/). Beyond these examples, I have found it very useful to write regression tests that can run prior to every release. The signup, login, add to cart and place order flow looks like this.

```
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

function router() {
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

router(signup,addToCart,checkout,makePayment,logout);
```

The router code is inspired by the router in Express. Along with a liberal use of phantom.injectJs to abstract common functionality, this helps us keep the code readable and avoid callback hell in phantomjs scripts. This is it - a brief introduction to phantomjs that will get you started, and as in my case, get you hooked.

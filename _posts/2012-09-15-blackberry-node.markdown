---
layout: post
style: text
title: Running Blackberry's Ripple Emulator on MacOS
---

Node.js is showing up in a lot of unexpected places. For a start, we are seeing less of 'node.js is cool' posts on hackernews, and more of stuff like 'why not node.js', or why 'golang is cooler than node.js'. I will [hold my decision on Go's production readiness](http://code.google.com/p/go/issues/detail?id=909) for now, and maybe even play with it someday, but I hope Go Lang isn't to C what Multics was to Unix.

Back to the subject of this post. I came across the Ripple Simulator while trying to build an html5 app for blackberry. It's a great chrome extension for mobile app developers (not just for blackberry), and a shame it won't run out of the box on MacOS. Well, it runs, but won't let you build and package apps out of the box. 

![https://chrome.google.com/webstore/detail/geelfhphabnejjhdalkjhgipohgpdnoc](/img/ripple.jpg "Ripple UI")

While trying to get it to run, I discovered that it uses node.js. It actually bundles node 0.5.4 (both mac and windows executables) in the .crx package. If you unzip the .crx package, inside the settings folder, you will discover a folder named node. Run the rbd service, and you will start seeing an option to build in chrome.

{% highlight bash %}
$ unzip ripple_ui.crx
$ cd services/node
$ ./node node_modules/rbd/app.js -start 9910
{% endhighlight %}


Node.js is surely going places. The subject of my next post - node.js on my Samsung TV ...

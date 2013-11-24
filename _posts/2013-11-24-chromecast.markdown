---
layout: post
style: text
title: Chromecast
---

My $35 chromecast from Google arrived this week. Except that after all the taxes and duties, it was no longer $35, more like $60 for me.

![Eureka, the dongle] (http://upload.wikimedia.org/wikipedia/commons/thumb/8/8b/Chromecast_dongle.jpg/300px-Chromecast_dongle.jpg)

Its a device google bills as the easiest way to enjoy online video and music on your TV.

Chromecast allows one to make the TV a second screen (the first being your mobile, tablet or laptop), and do things like playing youtube videos on TV. Well, that description is not entirely correct, because thats pretty much the only thing you can do if you aren't in the US. I can't use Netflix, Hulu or HBO Go, so I am limited to playing youtube videos. No cribbing, its my primary use case.

I desperately need my first screen back. My 1.5 year old has taken over my laptop, and the glorious apple mackbook has been reduced to a youtube viewer. There is hardly a rhyme that I have not since watched, and I am sick of youtube. In chromecast, there is hope that I can play the video in a tab and cast it off to TV, freeing up the laptop/phone for greater good.

Initial thoughts - its as much a walled garden as any iDevice. It shows promise here and there, and I am hopeful it will get rooted or Google will relax its grip, but till that time, its strictly a developers only device. Now on to what I have been able to (re)discover.

Chromecast uses a protocol called DIAL to discover the available devices on the local wifi network. Here's how the initial discovery takes place.

{% highlight javascript %}

"use strict";

var dgram = require('dgram');
var s = dgram.createSocket('udp4');
var message = new Buffer("M-SEARCH * HTTP/1.1\r\nHOST: 239.255.255.250:1900\r\nMAN: \"ssdp:discover\"\r\n" 
                        +"MX: 10\r\nST: urn:dial-multiscreen-org:service:dial:1\r\n\r\n","utf-8");

s.bind();

s.on('listening', function() {
  s.setBroadcast(true);
});

s.on('message', function(msg,rinfo) {
  console.log(msg.toString("utf-8"));
});

s.send(message,0,message.length, 1900,"239.255.255.250", function() {
  console.log('message sent, awaiting response');
});

{% endhighlight %}

From that momemnt onwards, one is to use the REST interface at http://chromecast-ip:8008/apps to control and communicate with the apps. 

To play a youtube video, for example, send this HTTP request.

{% highlight bash %}
curl -d"v=6VjmKnFlJm0" http://<chromecast-ip:8008>/apps/YouTube
{% endhighlight %}

To stop playback, send a DELETE to the youtube app.

{% highlight bash %}
curl -X DELETE http://<chromecast-ip:8008>/apps/YouTube
{% endhighlight %}

More to come as I play with it over the next few weeks.


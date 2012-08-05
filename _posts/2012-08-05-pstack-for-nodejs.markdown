---
layout: post
style: text
title: pstack for node.js
---


Those of you with a Solaris background would remember pstack well. For those who don't, 
it is a tool from the ptools family, which attaches to the process and prints an execution
stack trace. Like the other tools in the [ptools](http://www.s-gms.ms.edus.si/cgi-bin/man-cgi?pstack+1) 
family (pmap, pfiles..), its really awesome for debugging, and insightful as a poor man's profiler. 

Then there is [jstack](http://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jstack.html), 
a tool that prints stack traces from JVM. Which made me wonder, why should there not be a 
jsstack, to print a stack trace for node.js. After all, what is good for the goose is good 
for the gander, and the debugging proces could be much improved if we had a jsstack like tool for node.js.

With that thought in mind, and an idle Saturday Morning to spare, I cobbled up [jsstack](https://gist.github.com/3254449).

Run it as 

{% highlight sh%}
jsstack <pid-of-node-process>
{% endhighlight %}

and it shall reward you with a precious stack trace. And the bonus - there is only one execution stack (hail the mighty
event loop that saved us from mind boggling complexity of threads).

Here's some sample output from our express.js app under load.


  
{% highlight sh%}
#00 #<RedisReplyParser>.emit(reply, {"0":{"children":[5,6,7,8,136,156,64,171,189,225],"level":0},"2":{"children":[],... (length: 48545)) events.js line 49 column 39 (position 2021)
#01 #<RedisReplyParser>.send_reply(reply=#<Buffer>) /Users/qasim/Source/nodestore/node_modules/redis/lib/parser/javascript.js line 279 column 18 (position 10286)
#02 #<RedisReplyParser>.execute(incoming_buf=#<Buffer>) /Users/qasim/Source/nodestore/node_modules/redis/lib/parser/javascript.js line 223 column 22 (position 8158)
#03 #<RedisClient>.on_data(data=#<Buffer>) /Users/qasim/Source/nodestore/node_modules/redis/index.js line 422 column 27 (position 13173)
#04 #<Socket>.[anonymous](buffer_from_socket=#<Buffer>) /Users/qasim/Source/nodestore/node_modules/redis/index.js line 66 column 14 (position 2241)
#05 #<Socket>.emit(data, #<Buffer>) events.js line 88 column 17 (position 3036)
#06 #<TCP>.onread(buffer=#<SlowBuffer>, offset=458512, length=32217) net.js line 397 column 14 (position 9301)
{% endhighlight %}

If you are interested in how it works, it uses the built in V8 debugger in node.js to fetch a 
stack trace. Sends a SIGUSR1 to the the process to enable the debugger, and then uses the standard 
debugger client that ships with node to fetch a backtrace. This technique can also be used to 
enable code injection, e.g. for dynamically modifying configuration without restarting the process. 
The source is less than 50 lines - you can check it on [github](https://gist.github.com/3254449)

*This post is the second in [my](https://plus.google.com/116590445665454270527?rel=author)
series on [Profiling node.js apps](/2012/07/15/node-profiling/). Check out some other stuff I have
blogged about [here](/)*


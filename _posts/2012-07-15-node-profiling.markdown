---
layout: post
style: text
title: Profiling node apps
---

We recently had a problem with our node.js app. It was leaking memory. Not that this problem was biting us or anything. With a daily deployment cycle, we end up restartng every day and this would mask such issues. Fortunately, there are weekends and we don't do releases on weekends (mostly, because there are cases when the weekend is the best day for a release). The alarming memory growth on the weekend as evident in this chart alerted us there may be a problem.

![amon.cx](/img/memory.png "Up and Up it went")

There are a few lessons I have learnt in all those years pounding on the keyboard. One of them is this. Like the canary in the coal mine, bugs will often manifest in a subtle way, but if you chose to ignore them because you were busy working on something else, they will come back like a malignant tumor and take you down. Its always better to be paranoid and jump at the first warning signs. When it comes to bugs, catch them young, when you have only have a [snafu](http://www.urbandictionary.com/define.php?term=snafu), not when its a [fubar](http://www.urbandictionary.com/define.php?term=fubar).

Deciding not to ignore this one, I looked at some of the profiling options available for node. For this particular bug in question, going with the gut and doing a code review was what nailed it, but  in the process, I discovered some decent profiling options in node.js. This post is about connect's profiler, and subsequent ones will be about nodetime, v8 prof, dtrace and mdb based profiling.

Connect's  Memory Profiler
--------------------------

The easiest option by far is to enable the profiler that comes with connect (or express). If your app uses express, enabling the profiler involves adding the profiler middleware at the top.

{% highlight javascript%}
app.use(express.profiler());
{% endhighlight %}

Make sure you use this middleware before any other. It uses the [process.memoryUsage](http://nodejs.org/api/process.html#process_process_memoryusage) api and it tells you the usage before and after every request.

{% highlight vim%}
  GET /
  response time: 39ms
  memory rss: 788.00kb
  memory vsize: NaNgb
  heap before: 28.63mb / 65.99mb
  heap after: 29.71mb / 65.99mb
{% endhighlight %}

The NaNgb in front of memory vsize is because vsize is no longer exposed via the process.memoryUsage api. 

You would notice that heap after > heap before, even when there is no leak. This is because the garbage collection hasn't yet happened. This should not be a big downer though, because when there is a really big leak, you would see a relatively larger heap increase. When I suspect a certain URL invocation may be causing the leak, I will fire repeated requests of the same url using ab or siege, and generally get a clearer result.

If this confuses you however, there is a way out. You can force garbage collection to run just before taking the end snapshot, and then get more meaningful result. This is easier done than said with the --expose-gc option from v8. If you run node with this option, gc is exposed to you as a function, so you can invoke gc() and force garbage collection when you want it. Stating the obvious, don't use this in production.

 Let's modify the profiler code to take advantage of this flag. If you open node_modules/express/node_modules/connect/lib/middleware/profiler.js in an editor, you will see this code

{% highlight javascript%}
module.exports = function profiler(){
  return function(req, res, next){
    var end = res.end
      , start = snapshot();

    // state snapshot
    function snapshot() {
      return {
          mem: process.memoryUsage()
        , time: new Date
      };
    }

    // proxy res.end()
    res.end = function(data, encoding){
      res.end = end;
      res.end(data, encoding);
      compare(req, start, snapshot())
    };

    next();
  }
};
{% endhighlight %}

Edit it, and add a call to gc();

{% highlight javascript%}
    ...
    // state snapshot
    function snapshot() {
      if (typeof(gc) == 'function') {
        gc();
      }

      return {
          mem: process.memoryUsage()
        , time: new Date
      };
    }


    // proxy res.end()
    res.end = function(data, encoding){
      res.end = end;
      res.end(data, encoding);
      if (typeof(gc) == 'function') {
        gc();
      }
      compare(req, start, snapshot())
    };
    ...
  }
};
{% endhighlight %}

I have added it conditionally, because if you forget to run node with the --expose_gc flag, things would still work.

This is ugly, forcing gc on every request twice, but we are only profiling, so it would do. With this change, the results are much more precise.

{% highlight vim%}

  GET /
  response time: 39ms
  memory rss: 12.00kb
  memory vsize: undefined
  heap before: 12.83mb / 69.27mb
  heap after: 12.83mb / 69.27mb

{% endhighlight %}

Of course, if you don't use express/connect, you can use process.memoryUsage directly to snapshot pieces of code which you suspect of leaking memory.

Next post, I would try to cover nodetime.

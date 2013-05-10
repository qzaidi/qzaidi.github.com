---
layout: post
style: text
title: 10 tips to nodejs nirvana in production
---

We have been using node.js in production environments since a few years now, back when it was still in 0.4. We have used node for ecommerce, for ad-serving, as an API server and just about everything else, short of calculating the nth fibonacci number (we use GO for that sort of stuff, no kidding). When you run stuff in production, and at scale, there are lessons to be learned and insights to be gleaned, sometimes the hard way. This is a compilation of certain learnings that work for us, listed here in the hope that someone may find it useful. YMMV.

For the impatient, here is the tl;dr
* Don't reinvent the wheel, follow the unix way of doing things.
* Build redundancy at all levels.

## Use upstart

As with any high availability system, you need to make sure that your node process is up all the time, and it starts at boot time.
There are multiple options for this, from monit to custom sys V init scripts and what not, but we have stayed with upstart.

Upstart is not available everywhere, but our production environments has always been Ubuntu, where it is available by default. Here's how the upstart config looks like.

Using upstart is fairly simple, just place the config file in /etc/init. Here's how the config file looks like, 

{% highlight bash %}
description "Start and stop myserver"
author "Qasim"

env APP_NAME=myserver
env APP_HOME=/var/www/myserver/releases/current
#Node Environment is production
env NODE_ENV=production
# User to run as
env RUN_AS_USER=www-data

# Make sure network and fs is up, and start in runlevels 2-5
start on (net-device-up
          and local-filesystems
          and runlevel [2345])
# Stop in runlevels 0,1 and 6
stop on runlevel [016]

# automatically respawn, but if its respwaning too fast (5 times in 60 seconds, don't do that)
respawn
respawn limit 5 60

# make sure node is there, the code directory is there
pre-start script
    test -x /usr/local/bin/node || { stop; exit 0; }
    test -e $APP_HOME/logs || { stop; exit 0; }
end script
 
# cd to code path and run node, with the right switches
script
    chdir $APP_HOME
    exec /usr/local/bin/node bin/cluster app.js -u $RUN_AS_USER -l logs/$APP_NAME.out -e logs/$APP_NAME.err >> $APP_HOME/logs/upstart
end script

{% endhighlight %}

The -u switch is to set the uid of the node process, and -l and -e redirect stdout/stderr to files. The redirection can also be done on command line, but that won't play well with logrotation.

Place that file in /etc/init, and then use start to start the proces.

{% highlight bash %}

# cp myserver.conf /etc/init/
# status myserver
myserver stopped/waiting

# start myserver
{% endhighlight %}

To restart or stop
{% highlight bash %}
#restart myserver

#stop myserver
{% endhighlight %}

If you ever change the init script, stop and start afterwards, as restart would not re-read the config file.

### Use cluster for multi-core environments

A node process uses a single core, and there isn't anything like threads (thank god for that). To make use of all the cores, you have to use multiple processes, and the cluster module helps here. 

The manual page for cluster still says its experimental, but don't let that deter you. It has come a far way off from the earliest incarnations, and its a must for any production server.

What cluster does is to start multiple node processes and load balance among them. It does this almost magically, without you needing to change any code. 

As you can see in the command line we used in the init script, all we do is to wrap the app.js process with a script, and that brings in cluster. Its like forking a process in C after creating the listening socket.

This is how some basic cluster code looks like

{% highlight javascript %}
var numCPUs = require('os').cpus().length;
var cluster = require('cluster')
cluster.setupMaster({exec: 'app.js'});
for (var i = 0; i < numCPUs; i++) 
  cluster.fork();
{% endhighlight %}

### Use (re)cluster for zero downtime deployments

Even in environments where only one core is available, as in our EC2 setup where we autoscale with small instances (1 Compute unit), there are advantages to using cluster. For one, you can use it to support zero downtime deployments or restarts.

If you reload nginx after changing the configuration, the master process forks new workers and tells the old ones to stop accepting new connections, but let them server existing sockets before exiting. So no downtime is involved when nginx is reloaded. We do the same thing with our node apps, and to do this, we use an npm module called [recluster] (https://github.com/doxout/recluster). The bin/cluster wrapper makes this automatic, and when a USR2 signal is sent to the master, it executes a reload. In our deployment script, after deploying the code, we issue a reload by sending a signal. There is a module named recluster that is a wrapper over node's cluster module and does this. Our bin/cluster shell wrapper actually uses recluster to do this.

If you use nfs, you could use the filesystem instead of sending a signal to the process to do this. We use a file named restart that is touched when we want the cluster to be restarted. Inside the bin/cluster wrapper, we use fs.watchFile to watch the restart file, reloading the cluster. This is slightly more convenient than figuring out the master process id and doing kill -USR2 &lt;pid&gt;, and especially handy if you use nfs and want to restart multiple servers at once.

We have our own wrapper script for this, so if we run in development, we run as 

{% highlight bash%}
$ node app.js
{% endhighlight %}
then this simply changes to 

{% highlight bash%}
$ ./bin/cluster app.js
{% endhighlight %}

and the wrapper adds all the cluster functionality, without requiring you to change anything in the app.

### Heartbeat Checks

In spite of our best intentions, things go wrong in production. One recent issue we had caused node.js to use too much of CPU. On EC2, if you do that, you meet the same fate as Oliver Twist (The Boy who asked for more). Any spikes above a few seconds and you are penalized for being a noisy tenant via steal time, and the server becomes unresponsive.

In our autoscaling setup we have on EC2, we have a ELB -> nginx -> node.js stack, and when things start getting bad on any of the instances, ELB will detect iand launch new instances. While we have made setting up a new instance really fast, it still takes 2-3 minutes for the instance to come up, deploy code, compile stuff and then be detected as up by the ELB. Thats too slow for our setup, and for this reason, we built redundancy into every instance. Our cluster shell wrapper also checks for a heartbeat, and if a timeout happens, restarts the app. Here's how that code looks like

{% highlight javascript %}
  if (port) {
    process.env.PORT = port;
    util.log('will monitor port ' + port + ' for heartbeat');

    setTimeout(function() {
      setInterval(function() {
        var request = http.get('http://localhost:' + port, function(res) {
          request.setTimeout(0); // disable timeout on response.
          if ([200,302].indexOf(res.statusCode) == -1) {
            reloadCluster('[heartbeat] : FAIL with code ' + res.statusCode);
          } else {
            util.log(' [heartbeat]:  OK [' + res.statusCode + ']');
          }
        })
        .on('error',function(err) {
          reloadCluster(' [heartbeat]:  FAIL with ' + err.message);
        });

        request.setTimeout(10000,function() {
          // QZ: This is an agressive reload on first failure. Later, we may change it 
          // to reload on n consecutive failures
          reloadCluster(' [heartbeat]: FAIL with timeout ');
        });
      },heartbeatInterval);
    }, startupDelay);
  }
{% endhighlight %}

### Log rotation

Thou shall rotate your logs is a commandment most people follow. How do we do it? Simple, we use logrotate, which is already there on most linux systems.

Here's how our logrotate config looks like.
{% highlight bash %}
"/var/www/myserver/releases/current/logs/myserver.out" 
"/var/www/myserver/releases/current/logs/upstart" 
"/var/www/myserver/releases/current/logs/myserver.err" {
        daily
        create 777 www-data www-data
        rotate 7
        compress
        postrotate
                reload myserver >/dev/null 2>&1 || true
        endscript
}
{% endhighlight %}

Copy this file in /etc/logrotate.d/

When the logrotate cron runs, it reads the above configuration, renames the files specified here (myserver.out becomes myserver.out.1) and then signals our myserver process using the reload command.
When you run reload myserver, upstart will send a signal to the application. At that moment, the app should close its existing stderr , stdout and reopen them. 

{% highlight javascript %}
function reload(args){
  if(args !== undefined){
    if(args.l !== undefined){
      fs.closeSync(1);
      fs.openSync(args.l, 'a+');
    }

    if(args.e !== undefined){
      fs.closeSync(2);
      fs.openSync(args.e, 'a+');
    }

  }
}

process.addListener("SIGHUP", function() {
  util.log("RECIEVED SIGHUP signal, reloading log files...");
  reload(args);
});

{% endhighlight %}

args.l and args.e are the files to be used for stdout/stderr, specified by running the process with -l and -e option.

### Automated deployments with deploy.sh
Capistrano what? Who wants to run ruby to install node programs and add another dependency.

For deployments, we use a modified version of the [visionmedia deploy.sh script] (https://github.com/visionmedia/deploy). Its a shell script, so no prerequisites, and is quite flexible, so we use it in other non nodejs projects as well.

### Set listen backlog, max agents and max open files limit.

We use express as our web framework, and its a de-facto standard now. Express boilerplate code could do with some improvements, here is what we change at the bottom of a generated app.js

{% highlight javascript %}

app.configure('production', function() {
  app.use(app.router);
  http.globalAgent.maxSockets = 500; // set this high, if you use httpClient or request anywhere (defaults to 5)
});


http.createServer(app)
    .on('error', function(e) {    // do not fail silently on error
      if (e.code == 'EADDRINUSE') {
        console.log('Failed to bind to port - address already in use ');
        process.exit(1);
      }
    })
    .listen(app.get('port'), 'localhost', acceptBacklog, function() {     // accept backlog should be set high, and make sure you configure tcp as well
      console.log("Express server listening on port " + app.get('port'));
    });

{% endhighlight %}

### Redis with hiredis. 
Unless you are still in the dark ages ,running a very traditional LAMP stack, chances are you will use some nosql. My advice, don't bother with anything else, just stick to redis. In our case, we use redis for session storage, and as a caching layer for mysql and other data.

If you use redis in production, use it with hiredis driver. Without hiredis, we have seen redis choke the server even at moderate loads. Hiredis is an async implementation of the redis protocol, as opposed to a javascript implementation.

### Profile often, with the v8 profiler.

The v8 profiler is easy to enable, you just add the --prof switch to node.
It will save you a lot of time if you get stuck with memory or CPU issues, and even otherwise, its a good idea to know where the bottlenecks are.

{% highlight bash %}
$ node --prof app.js
{% endhighlight %}

This creates a v8.log file, which you can then process with tools like node-tick.

{% highlight bash %}
$ npm install -g node-tick 
$ node-tick-processor v8.log
{% endhighlight %}

Its also possible to programatically enable it, such that you enable it by sending a signal and then turn it off after collecting profiler data, but I haven't experimented with that and YMMV. My understanding is that the profiler runs anyway in v8, to enable it to find and compile hot code, so the overhead of --prof shouldn't be huge. We have run it in production and it has helped us with CPU issues. For memory leak, see my previous post on node-time.

### Enable GOD Mode with REPL

You can run a REPL inside your node app, and then connect to it via a socket

To enable [ REPL ] (http://nodejs.org/api/repl.html), create a file named repl.js

{% highlight javascript %}
var repl = require('repl')
var net = require('net')

module.exports = net.createServer(function (socket) {
  var r = repl.start({
    prompt: '[' + process.pid + '] ' +socket.remoteAddress+':'+socket.remotePort+'> '
    , input: socket
    , output: socket
    , terminal: true
    , useGlobal: false
  })
  r.on('exit', function () {
    socket.end()
  })
  r.context.socket = socket
}).listen(1337);
{% endhighlight %}

then require repl.js somewhere inside your app, say in app.js

Now you can connect to your app in production, view variables, change settings.

{% highlight bash %}
telnet localhost 1337
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
[88501] 127.0.0.1:57876>  var m = require('./lib/mymodule');
[88501] 127.0.0.1:57876>  util.inspect(m);
{% endhighlight %}

Remember, GOD mode is absolute power, and that comes with greatest responsibilities. You can mess things up.

If you use cluster, you should tweak the code example above to use a different port for each worker, or else you will have to telnet many a times and then hope to get the right process.

For the paranoid, you can run REPL on a unix socket as well, and use it with socat.

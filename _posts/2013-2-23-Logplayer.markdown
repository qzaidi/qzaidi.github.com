---
layout: post
style: text
title: logplayer, a non real time log analyzer
---
  
  
*** tl;dr - Most web log analyzers are real time, which isn't great when you want to retrospectively analyze and debug 
a problem. Thankfully, with a small script we can make them non realtime. ***

Non Real Time?
--------------

For real time log analysis, we use tools like apachetop or goaccess. They are very handy, because they give you some
visibility into otherwise monotonous common log files, telling you about bandwidth used, request per seconds, and 4xx/5xx
errors.

One high traffic website I am involved with recently had a DOS attack. When this happened, nginx started returning 502s
because the upstream servers stop responding. Very soon, the domino effect cascaded into a meltdown, as users retry and
hit refresh when they see a not very helpful 502 page, putting even more load on the system.

This forced me to write a quick and dirty script in nodejs, that reads from an existing log file and writes it back
to another file, preserving the rate. Because you control when you start playing the logs, real time analyzers like
apachetop can then be run on the new file to recreate a visual view of the problem as it occured sometime in the past.
Here's the script on github.

[ logplayer on github] (http://github.com/qzaidi/logplayer)

apachetop will report the time incorrectly, but the visual analysis is way more valuable than scanning the log file itself.
The script does little more than reading the logs from a certain time and writing them to another file at the same rate 
they were originally written. But one day, maybe it can be extended to work with log.io 

More from the github readme ...

logplayer
---------

Logplayer is a non real time web access log player. Why non real time? Because real time tools don't work when you want to analyze a problem past its occurence.

In conjunction with tools like apachetop and goaccess, this can come in handy - helps you figure out what was happening at a particular point in time when something went bad.

usage
-----
Let's say you had an incident at 11:40 PM on 22nd Feb, and you have the nginx logs in /var/log/nginx/access.log.2.

Here's how you would use logtailer

```
npm install 

node index.js /var/log/nginx/access.log.2 22/Feb/2013:11:38:00 | tee /tmp/access.log

apachetop -f /tmp/access.log
```

This will start playing the logs to apachetop, as if things were happening now. 

Make sure to remove /tmp/access.log after playing. It may be possible to use a fifo in place of real file, but not recommended for high traffic sites.

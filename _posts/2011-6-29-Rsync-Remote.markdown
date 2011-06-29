---
layout: post
style: text
title: Rsync tip for slow remotes 
---

If you are working remotely, rsync is a handy tool to sync up between local and remote copies. For example, even though version control allows you to code and check in on the local host and then run on remote, I don't always want to check in the code before I have compiled/tested it on the target solaris box, while I use a Mac.

Here is a nifty alias you can set up to sync between remote and local.
It assumes that your directory hierarchy has some directory named env at the top, and allows you to sync your working directory to the corresponding remote directory.
{% highlight bash %}
alias rcs='rsync -c *.[ch] foo@bar.com:$(echo $PWD | sed "s/.*env\/\(.*\)$/env\/\1/")'
{% endhighlight %}

Add it to .bashrc, setup password less ssh login, and type rcs every time you have to sync.

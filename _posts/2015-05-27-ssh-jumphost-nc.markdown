---
layout: post
style: text
title: ssh, jumphost and netcat
---

This is one quick tip about how to use transparent ssh proxying via a jumphost.

For a while, I have been using netcat to do this. Then when I eventually upgraded my macbook, it stopped working, and the error message 'Protocol Mismatch' was anything but helpful. Since it took me a while to find this fix, I thought I will put it here.

It took a while to realize that transparent proxying in ssh doesn't need netcat anymore. Its built in.

For a single line proxy, this works

```
ssh -t <jumphost> -t <destinationhost>
```

If you would want to automate this via ~/.ssh/config, here's an example.

```
host <destinationhost>
  user <username> 
  ProxyCommand ssh tpgateway -W %h:%p 2>/dev/null
```

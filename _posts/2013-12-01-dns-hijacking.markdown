---
layout: post
style: text
title: DNS - hijacking and circumvention
---

This post is a logical continuation of [my experiences with Chromecast](/2013/11/24/chromecast/).

I am not a movie buff. But I am allergic to others chosing for me. The 'other' in this case is spectranet, my ISP. They prevent me from using a DNS server of my choice.

The DNS servers I wanted to use were from unlocator. And what is unlocator, you may ask. I didn't know till last week myself. Let me quote straight from their FAQ.

>Unlocator is a so-called “smart DNS service”. Our service allows you to use your Netflix subscription as well as other services when travelling abroad.

If you are traveling to India, you know netflix won't run, because it hasn't yet come to this part of the world. Well, I still don't have it running on chromecast yet, but at least I can run it on my computer and phone using unlocator, despite Spectranet's DNS hijacking. 

If you are only interested in running Netflix outside of US, you should check out Unlocator ,Hola, or any of the paid/free VPS services (in that order), and use whatever works. This post shows how you can work around DNS hijacking, and for the purpose of demonstration, I will use netflix and unlocator.

But first, here's the mandatory, in your face *disclaimer*. I don't condone or encourage bypassing content restrictions. This post is about how people *traveling to India* can make netflix like services work without using slower, VPN based alternatives. 

Usually with unlocator, all you need to do is to change your DNS to the servers provided by them, and everything works. When I tried that, it didn't work for me. Thanks to some stupid DNS Hijacking.

If this has piqued your interest in dns hijacking, here is the wikipedia definition. 

> DNS hijacking is the practice of subverting resolution of DNS queries. 

See, its not very different from MITM kind of attacks, and in opposition to net neutrality. There are various ways in which your ISP may implement DNS hijacking, but the outcome is the same. No matter what you do, you can't change your DNS servers. That restricts freedom, and is a subversion free men should resist.

The first step is to figure out if your ISP does DNS hijacking. Go to [unlocator](https://unlocator.com/account/aff/go/GdEBdOkWBVJeRP6q) and sign up. Then follow the setup instructions (change your DNS servers), and you would see something like this on the dashboard.

![unlocator status](/img/unlocator.png)

If you see green in all the 3 rows, congratulations, your ISP is benign. On spectranet, I would see a cross in the first row. I first contacted unlocator support and they suggested that if I have really really changed my DNS, then my ISP must be hijacking DNS.

I wanted to be sure. Someone said if I lookup a non existent domain and still see it resolving, even after I changed settings to use some public DNS like Google(8.8.8.8) or OpenDNS, that's a clear sign. Not on spectranet, they do return an NXDOMAIN.

{% highlight bash %}
$host somenonexistentdomain.com
Host somenonexistentdomain.com not found: 3(NXDOMAIN)
{% endhighlight %}

However, they inspect all udp packets destined to port 53, so no matter which DNS server you query, your packet never gets out of the ISP's network unmodified and it never gets anywhere other than Spectranet's DNS. But they pretend that it did, and send you a response making it look like it did. That's like all roads leading to Rome, but the signs saying otherwise.

I could specify any DNS IP, even a non existent one, and it works on spectranet. Here's proof.

{% highlight bash %}
$host yahoo.com 8.8.8.8
yahoo.com has address 206.190.36.45

$host yahoo.com 1.2.3.4
yahoo.com has address 206.190.36.45

$host yahoo.com 71.82.93.104
yahoo.com has address 206.190.36.45
{% endhighlight %}

The IPs I have used above (except the first one) are randomly made up, and the probability of them running a DNS server is zilch.

There is a program called [ICSI Netalyzr at Berkley](http://netalyzr.icsi.berkeley.edu/), which you can run for further confirmation. It takes an awful amount of time, being 100% pure java, but it tells you a lot of things about your connection, DNS included. Here's what it tells me of spectranet.

>UDP access to remote DNS servers (port 53) appears to pass through a firewall or proxy. The client was unable to transmit a non-DNS traffic on this UDP port, but was able to transmit a legitimate DNS request, suggesting that a proxy, NAT, or firewall intercepted and blocked the deliberately invalid request.
>A DNS proxy or firewall caused the client's direct DNS request to arrive from another IP address. Instead of your IP address, the request came from 203.122.63.152.
>A DNS proxy or firewall generated a new request rather than passing the client's request unmodified.

Now that we know what's happening, here is how we circumvent this. Spectranet's method is based on inspecting and filtering UDP packets on port 53. If you could somehow change the port on which these packets get sent, they can be bypassed. Essentially, this requires

* A DNS server(forwarder) that listens on a port other than 53 (I use 10053 in my example).
* Telling your local resolvers to use that new port.

Its easier said than done, and I didn't find any way to make Mac's DNS resolver change the DNS port. resolve.conf supposedly allows you to use any port (8.8.8.8.53 is port 53 on 8.8.8.8), but nobody uses good old resolv.conf these days. I tried using IP port forwarding as well, to forward local port 53 to a remote port, but that didn't work. And it shouldn't, since ipfw doesn't modify the actual packet.

Here is the setup. Run a forwarding DNS server on an outside server, using a port thats anything but 53. Then run a local forwarding DNS server and make it connect to the remote server. Because traffic from your browser to DNS is now within your local network, the ISP can't do anything on this hop. The traffic from your local DNS to remote DNS is on a non standard port, so unless your ISP develops NSA like capabilities, they won't do anything there either. And you will circumvent the issue with hijacking.

![Flow diagram](/img/dnshack.png)

The ubercool forwarding DNS I used for the above setup is [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html). ~~If you are interested in knowing about dnsmasq configuration, let me know in comments and I will update the post.~~

**Update**

Here's a bit about the dnsmasq config that should get you started.

On the local Box, we run a dns server on port 53. You can start with a default config, and uncomment/update these lines

{% highlight bash %}
server=uuu.vvv.www.xxx#10053
listen-address=127.0.0.1,192.168.1.3
{% endhighlight %}

Here - 192.168.1.3 is the fixed address I have assigned to the local Box. Why so? Because I also want to run a DHCP server and disable the DHCP on my router. In case you want the goodies to be available on all devices on your network, such as your mobile phone / tablets, you would want to do so as well.

{% highlight bash %}
dhcp-range=192.168.1.50,192.168.1.150,12h
dhcp-option=3,192.168.1.1
dhcp-option=6,192.168.1.3
{% endhighlight %}

This is it. Now on the VPS, you have to run another instance of dnsmasq, and here you only need to run it on port 10053, and point it to unlocator's DNS

{% highlight bash %}
server=185.37.37.37 
server=185.37.37.185
port=10053
listen-address=*.*.*.*
no-dhcp-interface=*.*.*.*
log-queries
{% endhighlight %}

Adding the log queries at the end allows you to see if the DNS is really being used by chromecast. You should also remember that any entries in /etc/hosts on local box will be given priority by dnsmasq.

One more thing - I haven't tried this myself, but if chromecast will still not work, add an entry to your router to make traffic to 8.8.8.8 and 8.8.8.4 go via non existent routes. I have heard that chromecast will override local DNS settings from router and still try to connect to google's dns, but if they are unreachable (easy to achieve via a bad route on the router), it will fall back and use the DNS you want it to use.

----- 

Coming back to this setup, it's all rather inconvenient, because the circumvention requires 2 systems (your laptop for local dnsmasq and a remote box where you can run dnsmasq on a non standard port). My advice to unlocator would be to run an instance on non standard DNS port as well, for cases like these, and make it slightly easier for mass adoption. Then if your router was really open, you could run dnsmasq sort of thing on the router itself. Right now, I can't even get the spectranet provided wifi router to return a different DNS, but I guess I have work laid out for the coming weekends.

---
layout: post
style: text
title: Why cloudfront isn't ready for us in India yet.
---

Some time back, I did a post on how some ISPs (mine included) [don't allow users to chose their DNS](/2013/12/01/dns-hijacking/), why that lack of choice is bad, and how to circumvent. It seems my troubles with DNS continue, for here I am at the receiving end of this problem.

The 17% problem
---------------

Paytm.com gets a decent amount of traffic, most of which is from India. Here's what Alexa says about us
![Visitors on Paytm.com](/img/paytmvisitors.png)
which is no surprise considering India is our target audience.

Based on this, one would predict that cloudfront traffic split would be similar. That is, 98% of cloudfront traffic should be originating from India. But here is how that split looks like, with only 83% from India, and a big chunk from Europe.

![Traffic on cloudfront](/img/cloudfront.png)

I have no better explanation than public DNS being used, but I am not entirely convinced with this being the only explanation.

When you make a DNS request, your DNS server will usually forward it (recursion) to the authoritative nameserver for that domain.
The final, authoritative name server of the CDN provider will respond with an edge IP thats closest to you. But this is based on the location of your DNS server, because it knows nothing about the final client(you). <a href="#1">[1]</a>

Normally, this won't make much of a difference, because the DNS server which forwards the location is close to the originating client, and serves as a good approximation. But that is no longer true when a public DNS like OpenDNS is used. For example, since there is no OpenDNS server in India, anyone resolving via openDNS will be mapped to Europe or Singapore.

Does that mean we are condemned to live with the ISP DNS server, (and their shameless [advertising on NXDomain results](http://www.icir.org/christian/publications/2011-foci-dns.pdf)) ? Certainly not. There are two ways for CDNs to get around this.

The first is anycasting. Anycasting allows one IP address to be assigned to physically different servers, and uses BGP routing to direct traffic based on geo proximity. If you try pinging 8.8.8.8 from 2 different locations, you will see what I mean. For example, whether I ping this IP from my home computer in India, or my VPS in the US, I get  the same RTL of 10-20ms. This is not a violation of the basic laws of Physics, merely anycasting at work. Both OpenDNS and Google DNS use anycasting, with the difference being that while Google DNS has a location in India, OpenDNS doesn't.

This is key to chosing the right CDN - CDN's that support anycasting are less likely to have the kind of problem we saw with cloudfront. CDN's that don't support anycasting would use incorrect edges for people using public DNS.

Cloudfront doesn't use anycasting, but to be fair, they recently started supporting the other alternative to anycasting, the edns-client-subnet extension.<a href="#1">[2]</a>

edns-client-subnet asks DNS servers (e.g. 8.8.8.8) to send a truncated IP address of the actual requestor (your home IP), enabling the authoritative name server to return the correct edge location. <a href="#1">[3]</a>

I tested this with a patched dig client ([patched to support edns-client-subnet](http://www.cdnplanet.com/blog/which-cdns-support-edns-client-subnet/)), and cloudfront indeed supports it (see CLIENT-SUBNET in dig output below).


```
$./dig d2d80wugb8bq6c.cloudfront.net @ns-1258.awsdns-29.org +client=203.122.0.137

; <<>> DiG 9.9.3-P2 <<>> d2d80wugb8bq6c.cloudfront.net @ns-1258.awsdns-29.org +client=203.122.0.137
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58111
;; flags: qr aa rd; QUERY: 1, ANSWER: 8, AUTHORITY: 4, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; CLIENT-SUBNET: 203.122.0.137/32/24
;; QUESTION SECTION:
;d2d80wugb8bq6c.cloudfront.net. IN  A

;; ANSWER SECTION:
d2d80wugb8bq6c.cloudfront.net. 60 IN  A 54.230.174.152
d2d80wugb8bq6c.cloudfront.net. 60 IN  A 54.230.174.194
```

Since the 17% is from May 2014, I wonder why this hasn't helped.

The permanent 302 redirects
---------------------------

Our other big grudge with Cloudfront at this time is its behavior vis a vis 302 Redirects. Before I get to that, here's a simplified overview of how we used Cloudfront.

Our primary use case for CDN is to serve product images. Images are stored in their original, hi-res version, and are scaled based on the resolution and screen size of the client. Because there is a huge fragmentation in the mobile world (particularly in the Android world), there are different sizes we scale to, and we do this lazily except for the most common sizes.

Our setup looks like this.

![How image resize works](/img/process.png)

There are different pricing classes in cloudfront, based on what Geos you want to serve. There is no India only price class, so we chose the next best one, which include Asia, Europe and United States. 

To my surprise, we saw 45 different requests come from 45 different edge locations (no tiering) for each resource (which is what forced us to introduce s3 in the first place). Out of these 45 requests, only 3 were relevant (Cloudfront has only 3 edge locations in India), but we ended up paying 15 times for bandwidth (for transfer between cloudfront and upstream) and CPU (for dynamic resizing). If there was some sort of tiered setup, cloudfront could have fetched the image once and then distribute it among the edges, but they don't, and so we decided to put an s3 server in between.

Because resized images get cached in s3, one image would only get resized once (in theory).

In practice, our EC2 resizer continued to get hits from cloudfront, even when the image was cached on s3. This happens as the 302 redirect gets cached at Cloudfront. So once this happens, requests from cloudfront keep bogging down the poor EC2 box that has to resize the image on fly, and introducing the s3 store in between becomes of no use. As far as I knew, 302 was meant to be a temporary redirect, and I expect CDNs not to cache 302. If I wanted caching, I would return 301. Not so with cloudfront. And not just this, it doesn't allow us to specify the TTL for this response (although it does allow me to do so for 4xx and 5xx responses), and setting a TTL in the cloudfront config (for all responses) seemed to have no effect. This was a bummer, but to be fair, something that there documentation is explicit about.

The MTNL problem
----------------

Finally, a CDN is supposed to work closely with ISPs, even if those are entrenched, non competitive government ISPs, as long as they have a signficant market share. We found that MTNL servers had trouble resolving any cloudfront.net subdomains. Try it yourself if you are on an MTNL connection (apparently their DNS servers block request from outside, so non MTNL users can't see for themselves).

While the AWS team was quick to suggest a work-around, I think this is something we would have liked to know in the first place, not after we discovered this issue.

So that winds up my rant about why we couldn't make cloudfront work. I am still on the lookout for a CDN solution that has none of this problem and that isn't charging exhorbintantly for bandwidth (cloudfront is $170 per TB, so it isn't cheap either). We used to run our own edge location in India before (usign a simple nginx + geoip + proxy_cache, its trivial to run one) and it worked nicely. The only thing that didn't work was the bandwidth costing, in particular the overage charges from our provider.

-----------

<a name="1"></a>

1. This is what allows us to watch netflix from India in the first place. 
1. As per this update from AWS dated April 2014, [Cloudfront also supports EDNS-Client-Subnet](http://aws.amazon.com/blogs/aws/improved-cloudfront-performance-with-edns-client-subnet-support/). 
1. As per [A faster internet](http://www.afasterinternet.com/howitworks.htm), this has been implemented by Google, Bitgravity, CDNetworks, DNS.com and Edgecast. 

---
layout: post
style: text
title: Experiments with Pi Zero
---

Its almost the end of 2019, and that housekeeping week were I generally try to tick off items off the tech laundry list. One of those was to upgrade my wired network printer to be wireless. I tried to save a few bucks last year by not buying a wireless printer, which lead to low printer usage and print head replacement. The idea was always to upgrade it to wireless using CUPS running on a pi, and it remained an idea for an year.

The last time I bought a pi was 2014, and that wasn't a very successful endeavor. Not that the set up was complicated, but I hardly had any use I could put it to. Besides, I didn't order a case for it, and it gathered so much dust lying around,  when I finally came back to it, it was not usable (a capacitor came off). So this time, I didn't want to spend money until I was sure of RoI.

Now I have to say that the pi zero has been the best investment of mine for 2019, in terms of bang for the buck. I spent less than [2k INR for the pi](https://amzn.to/2uPwDtg), [Rs 300 for the casing](https://amzn.to/2QZpQ9a) and [500 bucks for the camera](https://amzn.to/2R1iZMo), ordered off amazon. Here's how I use it

1. I run CUPS for wireless printing on my [wired HP inktank 310](https://amzn.to/2R4zTdb). The cost differential between wireless and wired printers alone exceeds the BoM, and mind you, this is in spite of the pi zero costing a lot more in India than it should.

1. I added a camera and run motion, and use it to capture videos of feral visitors to our garden.

1. I run a [vpn client](https://gist.github.com/qzaidi/b85a4e0b5137167a54a4701f23df13f1) on it to selectively route traffic for the entire home. There are many uses for it besides watching geo-restricted videos.

1. I run a [pi.hole DNS](https://github.com/pi-hole/pi-hole) on it, and this blocks a lot of ads and saves bandwidth. Besides improving the privacy situation.

1. I run sshd on it, and can use it to remotely connect to my home network.

Do you pine for the days when men were men and ran their own email servers? I don't know about you, but I am going to get a bigger pi. The [internet apocalypse is a prophecy fulfilled already in parts](https://au.finance.yahoo.com/news/indias-top-court-rules-indefinite-083041088.html), and we better get prepared.

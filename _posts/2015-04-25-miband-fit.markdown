---
layout: post
style: text
title: A week with mifit
---

For about a week, I have been playing with mifit, the Fitness band from Xiaomi. This post documents that experience.

The Genesis
-----------

I have never been a fitness enthusiast, nor do I excercise regularly. That said, I gave in to exhortations from family (to put it mildly) to do something about it. Since you can't improve what you can't measure, the rational choice was to set a baseline, by buying a tracker.

![the fitness band](/img/xiaomi.png)

Among the companies that sell fitness bands, Xiaomi is the one to watch for. I am a big fan of Xiaomi already, as much any value conscious Indian could be. The [$20 price](http://s.click.aliexpress.com/e/v3ny3bY7Q) is a big draw (and a disruption for the likes of Jawbone and Fitbit). The fact that this is priced nicely for value shoppers signals to me a wider chance adoption in the tech community, and as one thing leads to other, it means a higher likelyhood of reverse engineering, getting the data out of walled garden and so on. 

I [ordered it off aliexpress](http://s.click.aliexpress.com/e/v3ny3bY7Q), and the process was super smooth. The seller I ordered from originally used China Post, and then reshipped via Singapore Post due to some battery/custom issues. The current listing for this product now states that the seller 'Can not deliver to India', so I am assuming it wasn't super smooth for the seller, and she fulfilled the order only to honor the initial acceptance and/or because of the strict penalities Aliexpress might be imposing on cancellations. That's quite a contrast from the scenario here in India.

The Data
--------

I started with a modest goal of 6000 steps. Miband is less accurate than google fit when it comes to tracking steps (as measured on my treadmill), but its  to track sleep is awesome and a marked improvement over fitbit flex.

![sleep data](/img/sleep.png)

I was able to [extract sleep and step data from the device](http://qzaidi.github.io/miband/mi_data.html), with a little help from the forums. If you see that data, you might notice that it didn't track sleep for two nights, and that was because I was sleeping in a moving train on those nights.

On newer android lollypop devices, you can use the miband to unlock your phone, and you can also set it to vibrate when you recieve calls. Currently, activity tracking is very limited (it can't track swimming or treadmill running).

There is more I intend to play with. For example, it would be awesome if I could make it vibrate programatically. Leaving that to some other post, its time to catch up on the 2 hours of sleep shortfall for today.

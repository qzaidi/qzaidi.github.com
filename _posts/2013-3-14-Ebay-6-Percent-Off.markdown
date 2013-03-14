---
layout: post
style: text
title: Pi Day Hack - Getting 6% off at Ebay.
---
  
tl;dr - Client Side validations for coupons missing at Ebay, so javascript kiddies get 6% off on anything.

A fundamental principle of web security is to not trust the client, yet it looks likes nobody ever gives a damn. So with these cool little hack, you can get 6% off on anything on Ebay.This post is not a dig at Ebay nor an endorsement of this hack. I could have done a similar post for credit card provider American Express (which worries me more), but nevertheless, here we go.

Ebay India is one company which uses coupons in an intelligent, targeted way. If they notice you haven't been to their site in a while, they send you an emailer with a coupon inside, placed cleverly at the bottom, with an expiry date very close, to create a sense of urgency. So much better than dumb mass mailers that most India Ecommerce sites send. If you have been to the site recently, on the other hand, you wouldn't be getting a mailer.

I got one such mailer recently, but the code had already expired. Because the Coupon Code box sits there so invitingly, I feel compelled to do a search for ebay coupons. We are buying a Raspberry Pi Case here, so no point looking for restricted, product specific coupons. Sure enough, there are lots of 6% off coupons, but they are all tied to a particular mode of payment. For instance, Pay with ICICI Bank Netbanking, and get 6% off. The idea makes sense - the cost of netbanking transaction is lower overall, because of nil risks of chargebacks and lower processing fee, and this strategy is a win-win for both PaisaPay and the Bank.

So I picked up one from ICICI Bank, and voila, Ebay congratules me for being a smart shopper. 

![ebay.co.in](/img/coupon-applied.png "Smart Move?")

Then I realize I am not so smart, because, the only payment method allowed is ICICI Bank netbanking, and while I do have an account with that bank, I hardly use the netbanking service.

![ebay.co.in](/img/restricted-payment.png "Netbanking is a net loss for me")

However, I decide to take my chances, and place my hopes in Javascript. So looking at form values being submitted in the javascript console, I can see what the server will see.

![ebay.co.in](/img/form-values.png "I love developer tools")

This is good enough, so let's remove the coupon code, 

![ebay.co.in](/img/remove-coupon.png "Lets make a dumb move.")

Surprisingly, they didn't call that a dumb move. 

Then I select my desired payment method. When you can borrow, why netbank. Some things are priceless, like this Rs. 15 saving via this hack. For everything else, we have Master card.

![ebay.co.in](/img/change-payment.png "Priceless.")

and then, we finally update the values via the console

![ebay.co.in](/img/form-values.png "I love developer tools")

Now all I needed to do is to click I am feeling lucky button. Not really - thats the Pay now button. But I do end up being lucky.

Very interestingly, I get charged for only Rs. 215 , but in the order history, it shows I paid Rs. 230.

Well, if you have read so far, you should listen to my rant as well.

I don't endorse doing this kind of thing, except for fun and profit. I deliberately did this test for low value items, because this is like stealing from someone who forgot to lock their doors. And yet, I feel like this is something I should write about, because in the quest for deadlines, somewhere we have forgetten that the clients are not to be trusted. People value what they can see. Most management types see a webpage, not javascript, not HTTP, not protocols and packets. Developers see this usually, but being lazy, they will do only what the boss will value.

Its time for when the bosses learn developer tools. And then get their hands dirty. Till then, javascript kiddies will have fun.

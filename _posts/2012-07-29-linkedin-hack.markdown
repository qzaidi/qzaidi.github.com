---
layout: post
style: text
title: Connect to anyone on LinkedIn
---

A couple of days ago I was urgently trying to get in touch with someone at Register.com, a domain name registrar. If you ask why - well thats an story worthy of its own blog post, but not one I can tell you right now. Anyway, as it happened, there wasn't anyone in my network, so instead of showing the familiar Connect button, linkedin was showing me options to get introduced or send inmails. I am a basic user, and getting introduced with a 3 degree seperation is such a timetaking process. 

Or is it? I have not quite figured out how this works, but it appears that there is a nice little hack that lets you connect to anyone on linkedin, whether they are in your network or not. As with all the hacks, this one will have diminishing returns, so I won't be surprised if it stops working by the time you are reading this. 

The key here is miniprofile. Every time you hover over a connections name, a small ajax call goes to linkedin to fetch a brief profile and display it in a hover box. If you look at the url, it would look like this

[http://www.linkedin.com/miniprofile?view=&vieweeID=1864505&context=nus](http://www.linkedin.com/miniprofile?view=&vieweeID=1864505&context=nus)

The key here is vieweeID. If you open this URL in a browser window, you would be able to Connect to me even if I am not in your network.
To try this, search for someone in LinkedIn whom you can't connect and only see an InMail Option for. Then find there vieweeID, modify the link above, and send them a connection request. That's it. My guess is that the miniprofile API isn't validated the same way rest of the stuff is. 

The vieweeID is also a reflection of how old you are on linkedin. The oldest working viewID's I could find were of [ Jean Luc Vaillant ](http://www.linkedin.com/in/jvaillant) and  [Eric Ly](http://www.linkedin.com/in/ericly), both co-founders at LinkedIn.

Interesting, isn't it? Let me know in the comments if this worked/didn't work for you.

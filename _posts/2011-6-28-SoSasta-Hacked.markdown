---
layout: post
style: text
title: SoSasta Breached, passwords out in plaintext 
---

##SoSasta.com was compromised today

Well, some of the most secure sites get compromised, so I almost ignored this email that landed in my
mailbox this morning. It talked about a 'potential' breach, and urged me to change my password at SoSasta
and all other sites.

> Hi SoSasta Subscriber,

> Over this weekend, we've been alerted to a security issue potentially affecting subscribers of Sosasta. We wanted to let you know that the issue has been brought under control and your accounts are secure. However, as a precautionary measure, we recommend that you change your SoSasta password immediately, by visiting the SoSasta website (Sign-In using your existing password, then click on Profile followed by Change Password). If you use the same email/password combination at other websites, we recommend you change those passwords as soon as possible, too.

> Please be aware that none of your financial information (Credit Card, Debit Card, NetBanking etc) has been compromised since this information is not stored on SoSasta, as per law.

> If you have any concerns or find any unusual changes in your SoSasta account, please contact our Customer Support team as soon as possible at 1800 103 2111 between 9.30 a.m. and 6.30 p.m. IST, Monday to Saturday so that we can review your account.

> You should know that we are working aggressively to prevent this from happening again. Sosasta takes security and privacy very seriously -- it's important to us to provide you with a safe shopping experience of the highest quality, and we will do everything possible to keep your trust. Please accept our apology for any inconvenience or concern we've caused.

> Sincerely,
> SoSasta Customer Support

It is one thing to be hacked, and another thing to be caught with your pant's down. Maybe I can ignore the fact
that they had directory listing turned on in apache, and that someone placed the sql dump inside WWW root, but 
how about clear text passwords? There is absolutely no justification for that. It takes some guts to then call the 
breach trivial, and to tweet about everything being safe.
The issue w.r.t the leaked database is solved and are safe. However, as a precautionary step, we recommend to change your login passwords.

Who knows the credit card information is not kept on file as they claim. Also, their privacy policy is quite different from others, making me think if this was changed in  retrospect. I do not find such terms in similar deal sites.

> THIS DISCLAIMER OF LIABILITY APPLIES TO ANY DAMAGES OR INJURY CAUSED BY ANY FAILURE OF PERFORMANCE, ERROR, OMISSION, INTERRUPTION, DELETION, DEFECT, DELAY IN OPERATION OR TRANSMISSION, COMPUTER VIRUS, COMMUNICATION LINE FAILURE, THEFT OR DESTRUCTION OR UNAUTHORIZED ACCESS TO, ALTERATION OF, OR USE OF RECORD, WHETHER FOR BREACH OF CONTRACT, TORTIOUS BEHAVIOR, NEGLIGENCE, OR UNDER ANY OTHER CAUSE OF ACTION. USER SPECIFICALLY ACKNOWLEDGES THAT FridayMedia IS NOT LIABLE FOR THE DEFAMATORY, OFFENSIVE OR ILLEGAL CONDUCT OF OTHER USERS OR THIRD-PARTIES AND THAT THE RISK OF INJURY FROM THE FOREGOING RESTS ENTIRELY WITH USER.

If you ever signed on to groupon, [check here if you were affected] (https://shouldichangemypassword.com/). The security researcher who found the breach has uploaded the hashed email database here, and lets you query if your email was on the list.

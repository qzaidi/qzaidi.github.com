---
layout: post
style: text
title: Vanity Metrics in Tech
---

I started working in 2001, which was a pretty bad time in some ways for software engineers to start working. Dot com bubble had burst in the US and ripples were being felt in India.<a href=“#1”>1</a> 

I turned out to be exceptionally blessed in our batch. As employers started withdrawing their offer of employments left and right, and even the company that hired us decided to shut shop and sell their India business to another, they took care that placement offers are also respected. (Gratitude to everyone involved). So I ended up joining Hughes Software Systems (HSS), even as I was offered a position by Jhonson Controls and I did work there for a month. 

As we transitioned to HSS, every other day there was news of layoffs everywhere else, and there was one at HSS itself, just a month post our joining. At these times, with the least amount of experience on team, I didn’t feel very secure. We also had a company wide pay cut. As I saw engineers from the most premium colleges struggling to find a  job, it was natural to worry about survival. So every month, I counted my years of experience, and felt happy as they kept on increasing.

Now, years of experience, as you would all agree, is probably the most vain of all vanity metrics, except if you are in the Government. The point is that even vanity metrics may not be vanity depending on the place / context. Survival has a value, esp in wartimes (or recession times or in bureaucracy), and years of experience can convey that value. And as a matter of fact, most still consider years of experience as a useful metric for promotions and hirings.

This brings me to the central theme of this article. As Paytm stock atrophies<a href="#1">2</a>, and this could be a blog post in itself), I was asked  if I could clarify what do I mean by vanity metrics. So here I will look at vanity metrics, from both level of an engineer, to that of a team and a company. And like other MVPs, I will do a part 2 and 3 and look for non vanity metrics too, if this one gets some traction.

Hughes was a CMM Level 5 certified company. It brought with us hallowed auditors who would tell us in hushed voices what a sin it is to be not able to re-use code, and it brought a couple of metrics around it as well. But the ones that where prominently measured at Hughes were Lines of Code and Defect Density. Defect Density was measured as bugs (of a certain severity) measured by QA engineers divided by the Kilo Lines of Code, so for example with 12 major bugs and 8k lines of code, you had a DD of 1.25. Actually that had a DD of 1.25.

Now you probably already know how superficial and meaningless KLOC and DD could be, and I am a firm believer in brevity. At this point, I invite you to read this [folklore from Apple on -2000 lines of code](https://www.folklore.org/StoryView.py?story=Negative_2000_Lines_Of_Code.txt). If you do come back and finish the article, It will prime you well for my own experience.

The first real project I had was writing 3G protocol stacks, which involved reading 3gpp docs (somewhat like RFCs, but way worse) and converting them into C code that can run on a RTOS. There were 4-5 L2-L3 protocols that had to be written, and I was assigned one of them. As a new engineer left to his own circumstances entirely due to providence, I had no supervision and wrote 8K lines of code, fathering 3 major bugs in the process. This was definitely subpar by company standards and the defect density was deemed very high, until I was enlightened by a guru that the cardinal sin was not the 3 major bugs, but it was in the denominator. Had I written my protocols with 35-40K Lines of code as everyone else, I would have not made such a fool of myself and achieved a very respectful looking defect density. One additional tip was to write a lot of code was not to use functions but macros, because somehow LOCs were counted after macro expansion, you could pretend that code is more performant because you are avoiding the overhead of a function call, and apparently the LOC counting happened after the macro expansion state in C. This was the hard way I learnt about my second metric.

To be continued

------------------------------------------------

<a name=“1”></a>
1.  Not surprisingly, I heard this from joyous civil engineering majors who for once felt happy that the comp sci guys will have a fate no different than theirs.
2.  I do not have a position, never had except in ESOPs which I couldn’t exercise (looks fortunate in hindsight)

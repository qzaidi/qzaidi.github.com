---
layout: post
style: text
title: Using Free Barcode fonts with pdfkit and nodejs
tags: barcode
---

## Using a barcode font with pdfkit

pdfkit is a nodejs library that makes generating complex pdf documents easy. Although its written in cofeescript, it does comes with great documentation, and it was easy to get it up and running. All I had to do was to convert the example coffescript program into node, and move around a zlib file. 

It was easy to get started, but I had a unique requirement. I needed pdfkit to print barcodes along with regular text, and doing that turned out to be non-trivial.

To cut the long story short, one way to generate those machine readable barcodes quickly and easily is to use a code 3of9 font. With a code3of9 font, you just print the barcode characters in plain old english, delimited by * on each side, and what you get to see is a barcode. There are several free and paid 3of9 fonts floating around on the web, but all the ones that I would try would cause pdfkit to throw an exception.

> No unicode cmap for font

In the end, an old usenet post came to the rescue, and here I am, posting it here so someone else using pdfkit or 3of9 fonts may save some time. Well, the problem isn't specific to pdfkit, it just seems that the fonts were generated badly, but if you regenerate them using ttx, they work flawlessly.

{% highlight bash %}
sudo apt-get install fonttools

ttx fre3of9x.ttf  # generates fre3of9x.ttx
ttx fre3of9x.ttx  # regenerates fre3of9x.ttf as fre3of9#1.ttf, which works with pdfkit

{% endhighlight %}

Update: Here's a link to download the modified font: <a href="/img/f39.ttf">Free of 39 Modified Font </a>

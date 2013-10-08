---
layout: post
style: text
title: A Magento Api client in nodejs
tags: 
  - magento
  - node.js
---

> Simple things should be simple, complex things should be possible.
>
>																			Alan Kay

This weekend, I spent an hour hacking togather a javascript client for magento's API. Considering that it was my first time using an XML RPC API, it reinforces my belief that nodejs will become the next php of internet programming (hopefully without the bad parts). 

Some day, I would love to convert this to use EventEmitter and post it on npm, but for now, here it is, as a gist on github. It uses the excellent xmlrpc client from baalexander, installable as 'npm install xmlrpc'.

<script src="https://gist.github.com/1289509.js"> </script>

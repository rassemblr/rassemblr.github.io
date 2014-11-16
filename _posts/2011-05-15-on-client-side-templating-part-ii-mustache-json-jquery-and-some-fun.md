---
title: 'On Client Side Templating Part II: mustache, json, jquery and some fun'
author: Pascal Deschenes
layout: post
permalink: /2011/05/on-client-side-templating-part-ii-mustache-json-jquery-and-some-fun/
dsq-thread-id:
  - 304702373
categories:
  - development
  - howto
tags:
  - ajax
  - javascript
  - jquery
  - json
  - mustache
  - rest
---

In my previous [post][1], I’ve promised to come up with a simple case to bring forth my points . So here we go, a simple
application which gets both json model and view off the server, render its content on the client side using
[mustache.js][2] then dynamically change the document model using jQuery. You might be interested in looking at the
result over my [sandbox][3].

 [1]: /2011/04/on-client-side-templating/ "On Client Side Templating"
 [2]: http://mustache.github.com/
 [3]: http://sandbox.rassemblr.com/cst/

<!-- more -->

## Model and View

First of, we have the following json model. In real life, instead of a static file, model would be returned from a REST
service request

Then, in order to separate content from the layout, we use a template view which contains the actual markup along with
data placeholders. While there are [better][4] alternatives, mustache is our template language for this basic example. 

 [4]: http://handlebars.strobeapp.com/

## Rendering

Rendering now becomes only a matter of applying this model into our template using the mustache library. In the
[controller][5], a method is defined to perform this operation:

 [5]: https://github.com/pdeschen/sandbox/blob/master/cst/js/controller.js

## DOM Update

Then, once you have the rendered markup, simply replace your dynamic content found in a div container and you should be
good to go!

Not a revolution but I really like this and I’m sure I’ll explore some more in that direction.

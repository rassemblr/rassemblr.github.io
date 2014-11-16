---
title: On Client Side Templating
author: Pascal Deschenes
layout: post
permalink: /2011/04/on-client-side-templating/
dsq-thread-id:
  - 290909053
categories:
  - development
  - thinking
tags:
  - ajax
  - javascript
  - json
  - template
---

While template engine on the server-side is nothing new, client side templating is a relative newcomer and some
[high][1] profile websites have used it for some time now. The basic idea behind client side templating is to not only
offload the server from rendering the template, but also to easily leverage existing web service APIs while
un-cluttering JavaScript code with markup.

 [1]: http://twitter.com

<!-- more -->


**Offloading the Server**

Obviously, the bigger load you add-on the server, the slower the response time. A while back, client side template
rendering was simply unthinkable because of the relative slowness of consumer desktops and browsers. Now that the
desktop has more HP while browsers gets speedier, thanks to efficient JavaScript engines such as V8, Monkeys, Nitro and
the like, delegating some CPU cycles off the server seems logic. We are simply moving from the *Request -> Render ->
Response* paradigm to the *Request -> Response -> Render* one.

Servers are now serving static views and models while the controller sits on the client.

**Un-clutter the JavaScript space**

How many time have you seen or been obligated to process strings of HTML within your JavaScript library? Workable but
ugly (*return ‘’ header ‘’;*). Separation of content anyone? I like the idea of having my JavaScript code completely
free of any markup. If this principle has always been good on the server-side, why should it be any different on the
client side?

**Leveraging Web Service APIs**

More and more organizations are now opening Web APIs to retrieve data information and this is a gold mine which we can
more easily leverage within our web apps. No need to set up a complex server architecture for that. Get data from a
given web service, get the template off your static file server, render, and then voilà. Data from various sources can
even be merged into a complex model. Imagine pulling data from the [data science toolkit][2] to get coordinate
information for a given IP address, then grabbing some photo URLs off [Flickr][3] and a map from [Google][4] for the
associated coordinates. All this using a bit of Ajax, templating, and some special sauce, **on the client side**.

 [2]: http://www.datasciencetoolkit.org/
 [3]: http://www.flickr.com/services/api/flickr.photos.geo.photosForLocation.html
 [4]: http://code.google.com/apis/maps/documentation/geocoding/#ReverseGeocoding

In principle, nothing exactly new here. Only the puzzle pieces are changing a bit.

Stay tune for a simple and bare bone proof of concept. **Update:** [here it is][5]: On Client Side Templating Part II:
mustache, json, jquery and some fun!

 [5]: /2011/05/on-client-side-templating-part-ii-mustache-json-jquery-and-some-fun/ "On Client Side Templating Part II: mustache, json, jquery and some fun"

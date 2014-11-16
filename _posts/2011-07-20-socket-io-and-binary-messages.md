---
title: Socket.IO and Binary Messages
author: Pascal Deschenes
layout: post
comments: true
permalink: /2011/07/socket-io-and-binary-messages/
dsq-thread-id:
  - 363873908
Hide OgTags:
  - 0
categories:
  - development
  - howto
tags:
  - base64
  - binary
  - express
  - html5
  - image
  - nodejs
  - socket.io
---

A couple weeks ago, @[cjoudrey][1] presented [Socket.IO][2] as part of the [NodeMTL][3] 
monthly meet-up. During the presentation, I had my head spinning over a few applications for 
this real-time message framework. Being into telephony and speech recognition, binary 
messages came to mind, audio to be more precise. While we argued that this sort of message 
format should be possible, I had to try it out both for my own curiosity and my little [pet project][4].

 [1]: http://twitter.com/cjoudrey "Christian Joudrey"
 [2]: http://socket.io/ "Socket.IO"
 [3]: http://nodemtl.com/ "NodeMTL"
 [4]: http://blog.rassemblr.com/2011/07/how-to-hack-a-road-trip-with-a-webcam-a-gsp-and-some-fun-part-i/ "How to Hack a Road Trip with a Webcam, a GSP and Some Fun: Part I"

<!-- more -->

## Server Side

Suppose we have some binary data on the server side that we want to push over the client 
side. An image would do the trick but most probably any kind of binary would do (on the 
server side, that is!). First, somewhere within the [express][5] app.js, we simply Base64 
encode the data before emitting the message. Notice that I use volatile since I don’t really 
care about occasional missing frame but your millage may vary.

 [5]: http://expressjs.com/

    io.sockets.volatile.emit('frame', {
      data : frame.toString('base64')
    });

## Client Site

While the server side does not present any complication, on the client side, things are 
less obviously unless you’ve already been exposed to the [Data URI Scheme][6] as specified 
by [RFC 2397][7] which

 [6]: http://en.wikipedia.org/wiki/Data_URI_scheme
 [7]: http://tools.ietf.org/html/rfc2397

> “[...] provides a way to include data in-line in web page as if they were external resources.”

All good for image but that would not work for video or audio for instance. Lets stick with image data.

    socket.on('frame', function (frame) {
      var img = $('#frame');
      img.attr("src", 'data:image/png;base64,'   frame.data)
    });

We add a socket handler for frame message reception, get the image element and assign the 
binary content using the data URI scheme. That’s it. Nice.

That being said, I wonder at this stage whether the [HTML5 audio ][8]or generally media 
elements could be leveraged on the client side. More things to explore!

 [8]: http://www.w3.org/TR/2010/WD-html5-20100304/video.html#audio

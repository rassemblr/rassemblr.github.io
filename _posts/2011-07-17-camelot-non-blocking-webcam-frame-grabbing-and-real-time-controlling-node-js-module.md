--- 
title: 'Camelot: Non-Blocking Webcam Frame Grabbing and Real Time Controlling Node.js Module' 
author: Pascal Deschenes 
layout: post 
comments: true 
permalink: /2011/07/camelot-non-blocking-webcam-frame-grabbing-and-real-time-controlling-node-js-module/ 
dsq-thread-id:
  - 360986037 
categories:
  - development
  - howto 
tags:
  - camelot
  - fswebcam
  - javascript
  - nodejs
  - webcam 
---

As part of my own summer of code (!), I’ve start working on a few node modules to support a little pet project of mine
involving a road trip to Newfoundland, a webcam, a GPS device and some fun to share with my family and friends (stay
tuned for more information).  Hence, my first piece of the puzzle: [camelot][1], a Non-Blocking Webcam Frame Grabbing
and Real Time Controlling node module providing access to a webcam device in a non-blocking fashion way.

 [1]: https://github.com/pdeschen/camelot "Camelot node module"

<!-- more -->

## How it works

Once grabbing starts ( *grab()* ), frames are periodically grabbed off the web cam ( */dev/videoX* ) depending on the
provided frequency option. Within Camelot, once a frame is grabbed, an event is emitted containing the binary frame.
Also, instead of relying on event emission, a callback can also be passed to the grab() function, which will apply the
callback upon frame grabbing.  As per convensional wisdom, an error event will also be emitted upon Error. An update()
can also be applied to modify any grabbing options such as resolution, rotation, colors, etc…

Requirements

This module assumes you have a working binary of fswebcam along with font management library and associated font path
definition (e.g. GDFONTPATH). This module has been solely tested on Ubuntu with a [Rocketfish 8MP USB HD
RF-HDWEB][2] webcam but should work with any webcam supported by the v4l library.

 [2]: http://www.rocketfishproducts.com/products/computers/RF-HDWEB.html

Examples

    var Camelot = require('./camelot.js'); 
    var camelot = new Camelot( { 'rotate' : '180', 'flip' : 'v' });
    camelot.on('frame', function (image) { console.log('frame received!'); }); camelot.on('error', function (err) {
    console.log(err); }); camelot.grab( { 'title' : 'Camelot', 'font' : 'Arial:24', 'frequency' : 1 });


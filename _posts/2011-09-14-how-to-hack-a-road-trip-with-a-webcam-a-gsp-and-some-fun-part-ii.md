---
title: 'How to Hack a Road Trip with a Webcam, a GSP and Some Fun: Part II'
author: Pascal Deschenes
layout: post
comments: true
permalink: /2011/09/how-to-hack-a-road-trip-with-a-webcam-a-gsp-and-some-fun-part-ii/
Hide OgTags:
  - 0
dsq-thread-id:
  - 414810647
categories:
  - development
  - thinking
tags:
  - couchdb
  - gps
  - kerouac
  - nodejs
  - roadtrip
  - webcam
---

Reporting back from my summer vacation trip to wonderful [Newfoundland][1]. This road 
trip hacking consisted of 2 block features as briefly depicted in [a previous posting][2]: 
a local component for grabbing webcam frames, capturing GPS data location, and storing 
everything in CouchDB, and a UI featuring remote interaction with said webcam along with 
real-time tracking and visualization capabilities. This post summarizes the lesson 
learned while presenting a few improvements for next year’s road trip. Mostly for my own use.

 [1]: http://maps.google.ca/maps/place?q=Newfoundland&hl=en&ftid=0x4b0ca3c27d2ff00f:0x64ec9db3d57639a8
 [2]: http://blog.rassemblr.com/2011/07/how-to-hack-a-road-trip-with-a-webcam-a-gsp-and-some-fun-part-i/ "How to Hack a Road Trip with a Webcam, a GSP and Some Fun: Part I"
<!-- more -->

## The Good Parts

Grabbing a webcam frame every 15 seconds during a 2.5 week journey involving an average of 
4h-drive per day leads to a large amount of data. Roughly 23Gig or 23093059688 bytes to be 
more precise :-) CouchDB has handle the ride flawlessly, without any glitch whatsoever. 
True, the use case involves mostly data write and I had only a few trivial views. I’ll 
get a better idea on the reads once I start working on that time-lapse movie!

The combination of node, express and socket.io was simply great to work with. Moreover, I 
had the webapp application running for several days without any glitch despite the fact 
that hardware devices (webcam and GPS) were involved.

## The Bad Parts

First of, at the very last minute, I’ve come to realize that using the iPhone for incoming 
remote connections would not work at all. Indeed, it seems that either my provider (or my 
incompetency!?) is blocking incoming traffic to reach my tethered on board laptop. Hence, at 
the very last moment, I had to brake down the architecture into 2 separate nodes, one slave 
responsible for solely handling the on board processing, that is, frame grabbing and GPS 
tracking, sending the information back to the master node (hosted remotely) whenever a 
user would be visiting the remote website. The master node would obviously have to deal 
with the UI aspect, serving the webapp itself. Well, that was the plan. Due to some 
instability with the node socket.io client, a bit of struggle in separating the responsibilities, 
and a razor tight schedule, I didn’t complete such architecture revamp. Shame on me for not 
having test such aspects way before!

Second, while having a laptop onboard the Westfalia was nice, it required mostly constant power. 
Even though we had a power inverter plugged into the cigarette lighter, the setup was a bit 
clumsy and we had some trouble keeping the power on, due in part by the fact that the 
inverter was a bit under-powered and the laptop hungry for power. At some point, I came to 
realize that a nice little rooted Android tablet featuring an embedded GPS chip could be used 
instead (or maybe that’s just a geeky reason to get one…).

## Improvements

* Finalize the master/slave node architecture revamp.
* Improve the webapp UI to offer frames in chronological order instead of randomly.
* Run the slave node on an Android device.
* Add the ability to snap a frame on demand from another device on board (i.e. iphone, ipod)
* Automatic brightness and contrast control setting based on time of day.
* Generate daily time-lapse videos and serve them through podcast feed.

Wait to see Kerouac 2.0 next year!

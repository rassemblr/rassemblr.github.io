---
title: 'How to Hack a Road Trip with a Webcam, a GSP and Some Fun: Part I'
author: Pascal Deschenes
layout: post
permalink: /2011/07/how-to-hack-a-road-trip-with-a-webcam-a-gsp-and-some-fun-part-i/
dsq-thread-id:
  - 360950932
Hide OgTags:
  - 0
categories:
  - development
  - howto
---

This summer of code (!) story began with my significant other shooting:

> G: “Hey I’d like to create a stop motion movie of our road trip to
> Newfoundland!”.
> 
> M: “Yeah that would be cool!”

<!-- more -->

Then we started to get excited hallucinate imagine this website, where our
friends and family members could interact with a webcam mounted on the top of
our [Westfalia][1] (yeah, that picture on the right side is me driving our VW
Westfalia 87), grabbing frames on-demand, adding coordinates from GPS device,
returning back frames, tweeting commands, tracking our waypoints, mashing with
the [Google Map API][2] or [OpenStreetMap][3], etc.

 [1]: http://en.wikipedia.org/wiki/Volkswagen_Westfalia_Campers 
 [2]: http://code.google.com/apis/maps/documentation/javascript/basics.html "Google Map API" 
 [3]: http://wiki.openstreetmap.org "Open Street Map"

While my girlfriend is responsible for the hardware (essentially webcam
mounting), I’ll be in charge of the software. So here is the plan, and I’ll
update as time goes by:

* Create a [node wrapper for webcam controller][4] 
* Create a [node client for GPS daemon][5] 
* Store periodical information within couchdb 
* Create a basic website [using express and socket.io][6] 
* Add realtime interactiveness on the website

 [4]: /2011/07/camelot-non-blocking-webcam-frame-grabbing-and-real-time-controlling-node-js-module/ "Camelot: Non-Blocking Webcam Frame Grabbing and Real Time Controlling Node.js Module" 
 [5]: /2011/07/bancroft-non-blocking-gps-daemon-client-for-node-js/ "Bancroft: Non-Blocking GPS Daemon Client for Node.js" 
 [6]: /2011/07/socket-io-and-binary-messages/ "Socket.IO and Binary Messages"

I will have part of the infrastructure hosted on my EC2 instance will some
others, obviously, will have to stick with us through out the road trip.

Then back to the initial purpose, that is, our stop motion movie, which I will
create from some of the periodically saved frame. I’ll also try to make it
automatic so that everyday on the road, a new stop motion video would be
available for all to view.

I don’t even know whether we will succeed or not (within the given time frame,
that is, before we set sail). Moreover, while I have a nice little webcam, I
don’t really know about the frame quality while on road. Lastly, since I don’t
really know how much 3G coverage we can get in Newfoundland, maybe the
interactiveness will be in and out (that’s why I’ll be storing frames and gps
waypoints within a local couchdb for later processing).

What a geeky couple we are!

**UPDATE**: [Part II][7]

 [7]: /2011/09/how-to-hack-a-road-trip-with-a-webcam-a-gsp-and-some-fun-part-ii/ "How to Hack a Road Trip with a Webcam, a GSP and Some Fun: Part II"

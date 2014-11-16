---
title: 'Bancroft: Non-Blocking GPS Daemon Client for Node.js'
author: Pascal Deschenes
comments: true
layout: post
permalink: /2011/07/bancroft-non-blocking-gps-daemon-client-for-node-js/
dsq-thread-id:
  - 365721720
categories:
  - development
  - howto
tags:
  - bancroft
  - geo
  - geojson
  - gps
  - gpsd
  - location
  - nodejs
---

[bancroft][1] is a [node][2] client for the gps daemon providing configurable 
non-blocking location tracking with [geojson][3] geometries compatible messages. 
Code available over on [github][4].

 [1]: http://en.wikipedia.org/wiki/Global_Positioning_System#Bancroft.27s_method
 [2]: http://nodejs.org/
 [3]: http://geojson.org/
 [4]: https://github.com/pdeschen/bancroft

<!-- more -->

## How it works

Upon object creation ( *new Bancroft()* ), bancroft connects to a running GPS daemon and 
send *?WATCH* and *?POLL* messages to receive messages (json type) back from the daemon, 
hence, from the GPS device, itself. Upon connection, a *connection* event is emitted along 
with daemon version and release details object. A *disconnect* event is emitted once the 
daemon is found off grid.

At any time, bancroft, keeps track of the current location along with information about 
current satellite status. A *location* event is emitted once a new location has been tracked, 
that is, whenever the latitude, longitude or altitude receive from the device differs from the 
currently stored information. A *satellite* event is emitted whenever a given satellite (PRN) 
changes status.

### Location Structure

    {
      timestamp: 1311296682000,
      latitude: 45.456445,
      longitude: -73.569651667,
      altitude: 28.9,
      speed: 11,
      geometries: {
        type: 'Point',
        coordinates: [ -73.569651667, 45.456445, 28.9 ]
      }
    }

## Examples

    var Bancroft = require('./bancroft.js');

    var bancroft = new Bancroft();
    bancroft.on('connect', function () {
      console.log('connected');
    });
    bancroft.on('location', function (location) {
      console.log('got new location');
    });
    bancroft.on('satellite', function (satellite) {
      console.log('got new satellite state');
    });
    bancroft.on('disconnect', function (err) {
      console.log('disconnected');
    });

## Features

* Real-time location events
* Real-time satellite state events
* Location data includes [geojson][3] geometries *Point* format.

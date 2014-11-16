---
title: 'Introducing nodast: Asterisk FastAGI Reverse Proxy on node.js'
author: Pascal Deschenes
layout: post
permalink: /2011/05/introducing-nodast-asterisk-fastagi-reverse-proxy-on-node-js/
dsq-thread-id:
  - 316857328
categories:
  - development
  - nodast
tags:
  - asterisk
  - fastagi
  - nodejs
  - proxy
---

What if you had the possibility of handling [FastAGI][1] requests to upstream server applications to load balance, fail
over, or dispatch based on AGI URI locations and parameters? Well, now you can! [nodast][2] to the rescue: an
open-source reverse proxy specifically designed for FastAGI communication requests build on top of [node.js][3], a
scalable and fast non-blocking, asynchronous event-driven networking platform.

 [1]: http://www.voip-info.org/wiki/view/Asterisk FastAGI 
 [2]: https://github.com/pdeschen/nodast 
 [3]: http://nodejs.org

<!-- more -->

While FastAGI remains one of the best alternatives to build Asterisk applications, uncoupling the platform with the
application itself, scaling over multiple FastAGI server applications remains a challenge. In upcoming posts, I will
present how nodast can help you easily achieve FastAGI scalability.

This project is [available over github][2], fork at will, submit patch through pull requests. Looking forward to your
feedback.

## How It Works

nodast receives [FastAGI][1] requests from [Asterisk][4] (or other nodast as a matter of fact), inspect incoming stream
for AGI request details. Looking more specifically at the `agi_request` parameter, the AGI URL is then matched against
the config route definitions, where route definitions values are either static or dynamic. Dynamic values are simply
JavaScript closure blocks, with /this/ pointing to a variable context containing all request parameters along with
regexp match group items. The dynamic function is simply rewriting the AGI URL, changing the destination hostname and
port as necessary. The resulting route is NOT re-injected back into route matching engine for further resolution.

 [4]: http://www.asterisk.org/

Once route has been resolved, nodast then look for upstream definitions and replace logical name with upstream
equivalent.

## Features

* Basic config definition with location and upstream 
* Basic command line test mode (`nodagi -t 'agi://foobar.com:8080/querystring'`) 
* Syslog Daemon init mode

## Upcoming Features

* Publish to npm repository 
* Add location re-injection 
* Add request logging details along with logging level in config.
* Add load balancing support between upstreams 
* Add failover/backup upstream Add SSL/TLS support between node (asterisk → node → ssl → node → fastagi server) 
* Add support for AMI Auto-Scaling with VM spawning Application Scripts

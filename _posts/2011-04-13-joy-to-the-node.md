---
title: Joy to the Node
author: Pascal Deschenes
layout: post
permalink: /2011/04/joy-to-the-node/
dsq-thread-id:
  - 278815531
categories:
  - development
  - thinking
tags:
  - asterisk
  - commonjs
  - javascript
  - nodejs
---

I’ve been experimenting with Node.js (or node for the intimate) [recently][1]. I was looking for something to execute
JavaScript within a shell to perform various web application development tasks such as offline templating, packaging,
testing, and such. And for an obscure reasons, Node seamed appealing.

 [1]: /2011/04/a-working-static-file-server-using-node-js/

<!-- more -->

My first look at Node goes back to some really early versions when I was looking at crafting a very thin rest service
over some of the [Asterisk][2] network protocol APIs (AMI and FastAGI not to name them). While I’ve abandoned this idea
(for now!), being too busy with other stuff,(**Update:** first release of [nodast reverse proxy][3]!) I had a good first
impression with node which got stuck in the back of my head. I always liked the idea of JavaScript on the server side.
Back in the old days of web 0.2, I did my first [freelance contract][4] using Netscape [LiveWire][5] on a Enterprise
Server. Spring 1997 if I remember correctly!

 [2]: http://www.asterisk.org/
 [3]: https://github.com/pdeschen/nodast
 [4]: http://babycenter.com
 [5]: http://en.wikipedia.org/wiki/Server-side_JavaScript

## **Super CLI**

So back to my use case: running JavaScript off the shell. While there are [other][6] [alternatives][7], Node seemed well
suited for the job. Moreover, leveraging packages offered through [npm][8] along with the recent adoption of
[commonjs][9], I could get up and running really quickly. Within a few hours, I was well into node, hacking my way to my
[coffeemaker][10] build system (stay tuned for upcoming release 0.0.1 over github!).

 [6]: http://www.mozilla.org/js/spidermonkey/
 [7]: http://www.mozilla.org/rhino/
 [8]: http://npmjs.org/
 [9]: http://www.commonjs.org/
 [10]: https://github.com/pdeschen/coffeemaker

## **Node as a Platform**

While experimenting with node, I came to realize that not only node offers a nice development model but also that it
provides a solid foundation as a development platform, featuring lots of traction from the community along with support
from [Joyent][11]. Through npm, you can install various shell utilities which spawn node, executing a given npm module.
While some modules are rather simple, some other are quite complex. Moreover, while some are really enhancing
node’s *build scalable network programs* motto, some others, like myself, are really into hacking [some][12] [neat][13]
[utilities][14].

 [11]: http://www.joyent.com/
 [12]: http://visionmedia.github.com/dox/
 [13]: https://github.com/chriso/cli
 [14]: https://github.com/reid/node-jslint

All in all, from my perspective, node has everything required to succeed in developerland:

*   A core language that is not only well-known but also widely adopted.
*   A rather large community.
*   Real world deployment success.
*   A rich and growing set of modules/libraries.

For my part, JavaScript seems fun again! Thanks to node.

**Updates:**

Some others share my views and beyond

* Troy Topnik in [“The secrets of Node’s success”][15] over [O’Reilly Radar][16].

 [15]: http://radar.oreilly.com/2011/06/node-javascript-success.html
 [16]: http://radar.oreilly.com/

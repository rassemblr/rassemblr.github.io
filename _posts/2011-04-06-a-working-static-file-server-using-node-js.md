---
title: A Working Static File Server Using Node.js
author: Pascal Deschenes
layout: post
permalink: /2011/04/a-working-static-file-server-using-node-js/
dsq-thread-id:
  - 273061797
categories:
  - development
  - howto
tags:
  - javascript
  - node.js
  - nodejs
  - server
  - static
---

I have recently been working on a pet project of mine running over [Node.js][1] – [coffeemaker: a JavaScript build
system][2]. During the course of crafting my project, I had to create a build task to serve static files for a target
web project. Being DRO (don’t repeat others!) oriented, instead of writing my own server, I have decided to look for
existing piece of code.

 [1]: http://nodejs.org/
 [2]: https://github.com/pdeschen/coffeemaker

<!-- more -->

My quest lead me to a plethora of code simple doing just that. In fact, this seems pretty much like the traditional
*hello world* for node. However, two things annoy me: most examples 1. [do not work][3] 2. [are useless][4]. Why not
provide content-type? Who on earth only serves plain/text?

 [3]: http://stackoverflow.com/questions/4720343/loading-basic-html-in-nodejs
 [4]: http://net.tutsplus.com/tutorials/javascript-ajax/learning-serverside-javascript-with-node-js/

Anyways, here it is, a fully working static file server in node.js. Grab the code and follow me on twitter [here][5].

 [5]: http://twitter.com/pdeschen

    var libpath = require('path'),
        http = require("http"),
        fs = require('fs'),
        url = require("url"),
        mime = require('mime');
    var path = ".";
    var port = 8088;
    http.createServer(function (request, response) {
        var uri = url.parse(request.url).pathname;
        var filename = libpath.join(path, uri);
        libpath.exists(filename, function (exists) {
            if (!exists) {
                response.writeHead(404, {
                    "Content-Type": "text/plain"
                });
                response.write("404 Not Foundn");
                response.end();
                return;
            }
            if (fs.statSync(filename).isDirectory()) {
                filename  = '/index.html';
            }
            fs.readFile(filename, "binary", function (err, file) {
                if (err) {
                    response.writeHead(500, {
                        "Content-Type": "text/plain"
                    });
                    response.write(err   "n");
                    response.end();
                    return;
                }
                var type = mime.lookup(filename);
                response.writeHead(200, {
                    "Content-Type": type
                });
                response.write(file, "binary");
                response.end();
            });
        });
    }).listen(port);


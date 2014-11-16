---
title: nginx Location-based Reverse-Proxy in Front of Apache
author: Pascal Deschenes
layout: post
permalink: /2010/12/nginx-location-based-reverse-proxy-in-front-of-apache/
dsq-thread-id:
  - 203438257
categories:
  - howto
tags:
  - apache
  - nginx
  - proxy
  - reverse-proxy
---

I’m currently experimenting with the [nginx][1] reverse-proxy feature where a request over a specific location would be
forwarded to some [Apache][2] web server listening on distinct ports as depicted in the *Figure 1* below.

 [1]: http://nginx.org/en/
 [2]: http://httpd.apache.org/

<!-- more -->

![nginx proxy](/images/nginx-proxy.png)
Figure 1 : nginx proxy with apache back-end

While the proxy part is well [documented][4], I have struggled a few hours (well ok, not that much) to get the
location-based reverse proxy to work properly. This post uses Apache for simplicity sake but a similar configuration
could be applied to some other web back-end such as [Rails][5], [django][6], [Apache Tomcat][7], [node.js][8], and the
like.

 [4]: http://wiki.nginx.org/HttpProxyModule
 [5]: http://rubyonrails.org/
 [6]: http://www.djangoproject.com/
 [7]: http://tomcat.apache.org/
 [8]: http://nodejs.org/

**Apache Configuration**

On the Apache back-end, nothing special needs to be done besides setting up virtual hosts for every back-end services.
The following `serviceA.conf` is an example for a given *serviceA* back-end. The same applies to service B and C, using
port 89 and 90 respectively.

**nginx Configuration**

nginx needs to be configured not only as a proxy but also with [upstream][9] server definitions over which requests
should be dispatched. The `services.conf` below illustrates our case for service [A][10], [B][11] and [C][12] with three
upstream definitions, a base proxy configuration, along with location-based specifics. 

 [9]: http://wiki.nginx.org/HttpUpstreamModule
 [10]: http://labs.rassemblr.com/serviceA/
 [11]: http://labs.rassemblr.com/serviceB/
 [12]: http://labs.rassemblr.com/serviceC/

Coming up next: what about adding SSL to nginx?

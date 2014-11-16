---
title: 'REST Communication with Ajax jQuery: a Bumpy Road'
author: Pascal Deschenes
layout: post
permalink: /2011/05/rest-communication-with-ajax-jquery-a-bumpy-road/
dsq-thread-id:
  - 309401128
categories:
  - development
  - thinking
tags:
  - ajax
  - authentication
  - jquery
  - rest
  - webapi
---

My latest development project involved providing a new RESTful Web API to one of our [product][1]. While providing the
API has proved to be relatively painless (thanks to [RESTEasy][2] and our SOA design!), the communication process has
been somewhat challenging, especially on the [jQuery][3] front. Part of the solution itself is not crystal clear at the
moment so here are simply my notes so far, which I’ll update once I have a clean and complete solution. Do not hesitate
to comment as you see fit.

 [1]: http://www.nuecho.com/nubot
 [2]: http://docs.jboss.org/resteasy/2.0.0.GA/userguide/html/
 [3]: http://jquery.com/

<!-- more -->

## Requirements

As always, we tend to keep the initial requirements slim with some room for the future. First and foremost, the REST
services need to be remotely available from a service client, within a fat Eclipse plug-in. Second, we wanted to secure
the communication channel using a mix of SSL and access/secret key authentication token pair, pretty much like a
username/password combination. However, as we intend to offer some of the services’ functionality through a web
interface in a near future, we also wanted to have a browser-based thin client, with the same minimal security
specification. Finally, we wanted to adhere to the REST principles as much as possible (I should probably blog about
such principles since I have found that not so many REST API appears compliant, but that is another story).

## Implementation

While we wanted to offer a REST api within our product, we didn’t want to deal with user management and authentication
within the web service itself. One way to circumvent this would be to delegate authentication to our [reverse proxy][4]
([nginx][5] in our case). Since we were also using SSL to encrypt the wire, using [http basic access auth][6] seemed a
reasonable solution at first thought, painlessly available [straight from within nginx][7]. Communication between the
proxy and the services are trusted and hence don’t need authentication nor SSL at all. While we didn’t experienced any
problem with such solution using the Apache *httpclient* library within our Eclipse plug-in, we did hit a few bumps on
the web browser front using jQuery. Hence, this post.

 [4]: /2010/12/nginx-location-based-reverse-proxy-in-front-of-apache/
 [5]: http://nginx.org/
 [6]: http://en.wikipedia.org/wiki/Basic_access_authentication
 [7]: http://wiki.nginx.org/HttpAuthBasicModule

### Cross-Domain Restrictions

One might be aware that ajax requests must be performed on the same domain, which is often referred to the [Same Origin
Policy][8]. While such limitation can be worked around using [JSONP][9] requests through a basic wrapping Servlet
Filter, there are no elegant solution regarding POST requests (aside possibly an [iFrame][10] [technique][11], but we
are getting in a dirty area in my opinion and this won’t work for posting *octetstream* data for instance.). If you have
control over the servers (from domain A and domain B), and you don’t care about some browsers (i.e. != Firefox 3.5,
Safari 4, Chrome 2), you could add a [CORS][12] response header in the form of *[Access-Control-Allow-Origin][13]: *.
But then again, if you have control over both servers, why not take the reverse proxy route? Looks like a new W3C
standard is [taking shape][14] to address some of those issues. First bump. Not that bad. We took the reverse-proxy
route. Done deal.

 [8]: http://en.wikipedia.org/wiki/Same_origin_policy
 [9]: http://en.wikipedia.org/wiki/JSONP
 [10]: http://thomas.bindzus.me/2007/12/24/adding-dynamic-contents-to-iframes/
 [11]: http://softwareas.com/cross-domain-communication-with-iframes
 [12]: http://hacks.mozilla.org/2009/07/cross-site-xmlhttprequest-with-cors/
 [13]: http://alexn.org/blog/2011/03/24/cross-domain-requests.html
 [14]: http://www.w3.org/TR/cors/

### Authentication

Then, we wanted to enforce authentication through basic access auth over SSL. While jQuery supports such
username/password tokens through [ajax][15] requests, browsers seem to handle the authentication failures (401) on its
own, popping its own ugly login dialog,  instead of delegating to the [XMLHttpRequest][16] client’s error handling, this
and a combination of the [WWW-Authenticate][17] header response. Which is plain odd and certainly undesirable from our
perspective. One of the reason why we were planning on using HTTP Auth was related to the fact that we didn’t want to
manage user session on the service side plus, it is against rest conformance about state transfer.

 [15]: http://api.jquery.com/jQuery.ajax/
 [16]: http://en.wikipedia.org/wiki/XMLHttpRequest
 [17]: http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.47

> *“Each request from any client contains all of the information necessary to service the request, and any session state
> is held in the client” – *[Representational State Transfer][18], Wikipedia

 [18]: http://en.wikipedia.org/wiki/Representational_State_Transfer

Second bump. Why some of those bits seem obvious, it looks like we needed a bigger 4×4 to get over this bump. But we
managed to circumvent this problem as well, thanks to some nginx and jQuery tricks. [Here is how it has been done][19].

 [19]: /2011/05/jquery-ajax-and-rest-http-basic-authentication-done-deal/ "jQuery Ajax and REST HTTP Basic Authentication: done deal!"

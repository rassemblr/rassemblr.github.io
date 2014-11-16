---
title: 'jQuery Ajax and REST HTTP Basic Authentication: done deal!'
author: Pascal Deschenes
layout: post
comments: true
permalink: /2011/05/jquery-ajax-and-rest-http-basic-authentication-done-deal/
dsq-thread-id:
  - 310892633
Hide OgTags:
  - 0
categories:
  - development
  - howto
tags:
  - ajax
  - authentication
  - jquery
  - nginx
  - rest
---

> “I’m talking **done deal**, closed case
> Ain’t no time for looking back on the road you take
> You gotta claim your spot, nobody hold your place
> Ain’t no time to preach about time you chose to waste
> You gotta mold your fate, hold your weight” Done Deal - Lil’ Wayne

As [previously mentioned][1], while we did hit a road bump during our REST authentication implementation, 
we are now comfortable with the following solution. A live demo should be available soon.

<!-- more -->


 [1]: http://blog.rassemblr.com/2011/05/rest-communication-with-ajax-jquery-a-bumpy-road/ "REST Communication with Ajax jQuery: a Bumpy Road"

## Client-Side

On the client-side, the basic idea (directly inspired by Aswin Anand’s [solution][2]) is to 
craft the Authorization request header in a *beforeSend* callback instead of relying on jQuery’s 
own username/password mechanism. Using such seems to circumvent the browser login dialog. As per [RFC 1945][3], 
the Authorization header value should contain the *username:password* as encoded (base64) string, which 
would result in something like the following header:

 [2]: http://www.aswinanand.com/2009/01/http-basic-authentication-using-ajax/
 [3]: http://tools.ietf.org/html/rfc1945#section-11.1

`Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`

While there are various JavaScript API out there for base64 encoding, we have settled so far with [crypto-js][4]. 

**WARNING**: Note that such encoding does not warrant for a secure connection. Packets going over the bare bone wire could 
be sniffed and the username:password string can be easily decoded. To enhance such authentication security, SSL 
communication `must` be used. 

Obviously, that is what we are using.

 [4]: http://code.google.com/p/crypto-js/

## Server-Side

On the server side, on top of the [AuthBasic][5] module, we rely on a neat nginx [trick][6] (which I’m 
sure is also available on Apache). Indeed, upon access denied, without this minor tweak, the server would 
return a 401 HTTP status code (Unauthorized) along with a WWW-Authenticate header. While this corresponds 
to the expected behavior as part of the RFC, this combination triggers the browser login popup dialog, 
which does not really blend in terms of UX.

 [5]: http://wiki.nginx.org/HttpAuthBasicModule
 [6]: http://wiki.nginx.org/HttpCoreModule#error_page

![](/images/browser-login-dialog.png)

In order to circumvent this aspect, instead of returning a 401, a 403 (Forbidden) status code 
is used, which seems fair enough from our perspective. Hence, we ended crafting the following 
nginx configuration:

## Other Considerations

We still have to figure out some details like the following:

*   Session Timeout
*   Username Password Cookie Persistence
*   Direct Resource Access Redirect

Another upcoming blog post should cover those aspects.

Stay tuned or follow me on twitter [here][8].

 [8]: http://twitter.com/pdeschen

`UPDATE: it appears that this blog entry is one of the most popular among my blog visitors, and I'm really glad 
for it if this post could be of any help. That being said, I'd like to hear from you! Whether it helped or not, 
leave a comment and your thoughts about this solution. Or ping me on twitter.`

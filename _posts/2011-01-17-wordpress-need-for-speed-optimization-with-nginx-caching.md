---
title: 'WordPress Need For Speed: Optimization with nginx Caching'
author: Pascal Deschenes
layout: post
permalink: /2011/01/wordpress-need-for-speed-optimization-with-nginx-caching/
dsq-thread-id:
  - 211113023
categories:
  - development
  - howto
tags:
  - apache
  - caching
  - nginx
  - proxy
  - wordcampmtl
  - wordpress
---

Because my previous post ([User Registration Follow-up Email: A Checklist][1]) has generated a 
lot of traffic coming from [Hacker News][2] and then [twitter][3] afterward, my microscopic EC2 
instance literally died under the load.  While a three-hour ride on the Hacker News front page is 
exciting in itself, I had to find a way to minimize the HNed effect for future hit and…for fun:-)

 [1]: http://blog.rassemblr.com/2011/01/user-registration-follow-up-email-a-check-list/
 [2]: http://news.ycombinator.com/
 [3]: http://twitter.com

<!-- more -->

Being a (recent) fan of [nginx][4], I have decided to craft a caching configuration using its 
caching and proxying feature.

 [4]: http://nginx.org/en/

## IP forwarding with `mod_rpaf`

A reverse proxy is pretty straightforward to setup as per a previous post of mine ([nginx 
Location-based Reverse-Proxy in Front of Apache][5]). However, [analytics][6] wise, 
I wanted a solution where the `REMOTE_ADDR`, that is, the client IP, would stick around 
and be forwarded to Apache. Thomas Eibner’s Apache [mod_rpaf][7] to the rescue. However, 
most Linux distributions do not provide any sort of package for it so I ended up building it from scratch:

 [5]: http://blog.rassemblr.com/2010/12/nginx-location-based-reverse-proxy-in-front-of-apache/
 [6]: http://www.kissmetrics.com/
 [7]: http://stderr.net/apache/rpaf/

Now ready to go forwarding requests in.

**nginx configuration: base**

nginx configuration is two folds. First we have to enable the proxy caching related stuff 
(and why not gziping while we are at it?) in the *http {}* context. In */etc/nginx/nginx.conf*:

Obviously, adjust parameters to your own need and related configuration.

**Nginx configuration: WordPress related**

Then we have to fine-tune caching and proxying base on *location {}* rules for our 
WordPress *server {}*. In */etc/nginx/conf.d/blog-wordpress.conf* :

A keen eye might have noted at this stage that we are using port 81 on the nginx side and 
80 on the apache one. This configuration is for testing purpose only, minimizing impact on a 
running Apache server in case things turn sour. At this point, you should have nginx proxy 
listening on port 81 without any modification on the Apache side. Hence, you should be able 
to point your browser at http://blog.foo.bar:81 and get the same front page. Great!

**Apache configuration**

Finally, a few tweaks for the associated Apache virtual host configuration. To do so, simply 
swap ports in nginx and change your Apache virtual host *Listen 80* directive to 81 along 
with your virtual host definition itself to 81. Or whatever free port you have on your system 
if 81 happens to be already used.

**Testing**

In my opinion, a solution is not complete until it has been tested. So, how do you test such caching feature? 
Nginx features a variable (*[upstream\_cache\_status][8]*) containing the *HIT* or *MISS* details 
which can be used within a log format, and hence, dumped into a log file for review (See *log\_format up\_head* 
instruction above). [Curl][9] seems the perfect user-agent for the job! 

 [8]: http://wiki.nginx.org/HttpUpstreamModule#.24upstream_cache_status
 [9]: http://en.wikipedia.org/wiki/CURL

**Gotcha**

* Purge your cache directory once you’re done debugging or whenever you feel something odd happens during the 
  configuration process. You may configure the [nginx purge module][10] to do so.
* No need to rewrite URL on the nginx side.
* If you can, you should disable server-side comment rendering. Otherwise, comments will be cached as well.
* What about WordPress friendly URL rewrites? You don’t really care for those. Apache is already handling 
  rewriting for you.

 [10]: https://github.com/FRiCKLE/ngx_cache_purge/

**Areas for Improvement**

* This technique could be used with several *upstreamserver* to load balance requests to more then one apache server. 
  Hence, whenever you get a rush, you could fire a new EC2 instance to handle the load (this would obviously require 
  more sophistications using Elastic IP, shared database, and such).
* Instead of directly serving static files (WordPress Media content), we could delegate those requests using a CDN or S3.
* Stats to show off/benchmark the improvement would be nice!

Those should be the subject of some other posts! Stay tuned!

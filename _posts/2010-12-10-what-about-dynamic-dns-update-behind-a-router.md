---
title: What about Dynamic DNS update behind a router?
author: Pascal Deschenes
layout: post
comments: true
permalink: /2010/12/what-about-dynamic-dns-update-behind-a-router/
dsq-thread-id:
  - 203437419
categories:
  - howto
tags:
  - aws
  - dyndns
  - geoscaling
---

Recently, I have been migrating my personal infrastructure over an [Amazon EC2][1] Micro Instance. In 
the process, I have also changed my DNS provider and now rely on the excellent [Geoscaling][2] DNS2. 

<!-- more --> 

Geoscaling is not only snappy with short TTL but also offers a [basic update API][3] for A (and AAAA) 
record types. Using this technique, my EC2 instance now automatically updates its ip with the DNS upon relaunch. Cool stuff.

 [1]: http://aws.amazon.com/ec2/
 [2]: http://www.geoscaling.com/
 [3]: http://www.geoscaling.com/dns2/wiki/dynamic_dns_api

![][4]

 [4]: http://blog.rassemblr.com/wp-includes/js/tinymce/plugins/wordpress/img/trans.gif "More..."

But then, I wanted to have my home server to do the same thing. While my home router provides a mechanism by which it can update DNS record from a few well-known Dynamic DNS providers (such as [DynDNS][5] and [EasyDNS][6]), it CAN’T update records from Geoscaling. To work around this problem, I have developed a stupid rudimentary Ruby CGI script, which [simply returns the IP][7] of the http client:

 [5]: http://www.dyndns.com
 [6]: http://www.easydns.com
 [7]: http://labs.rassemblr.com/go/ip

Then, on my home server, I’ve hacked an rc.d ip-up cron script which simply perform a GET request using a mix of Geoscaling API and Curl. 

Note: you can hit my server to get your ip but I might remove it if I get to much abuse.

--- 
title: Some corporate filter proxies blocking Amazon S3 static website?
author: Pascal Deschenes 
layout: post
permalink: /2011/06/some-corporate-filter-proxies-blocking-amazon-s3-static-website/ 
dsq-thread-id:
  - 338881587 
categories:
  - development
  - thinking
tags:
  - aws
  - cname
  - s3
  - static
  - website
---

Recently, Amazon has introduced a feature within their S3 infrastructure: the ability to [directly serve a full static
website][1]. Prior to that, we had to set up a [Cloudfront][2] in order to set [default Object ][3]for a default
index.html. While this is all good news, it appears that using such setup might not be [as a good idea as it first
sounded][4].

 [1]: http://aws.amazon.com/about-aws/whats-new/2011/02/17/Amazon-S3-Website-Features/ 
 [2]: http://aws.amazon.com/cloudfront/ 
 [3]: http://aws.amazon.com/about-aws/whats-new/2010/08/05/cloudfront-adds-default-root-object-capability/ 
 [4]: http://blog.rassemblr.com/2010/12/simple-amazon-s3-cloudfront-static-web-hosting/ "Simple Amazon S3 CloudFront Static Web Hosting"

<!-- more -->

The steps to host a full-blown static web through Amazon S3 are simple and[ throughly explained][5]:

 [5]: http://aws.typepad.com/aws/2011/02/host-your-static-website-on-amazon-s3.html

1.  Create an S3 bucket named after your CNAME (e.g. www.foo.bar) 
2.  Setup permissions for this bucket as world
viewable 
3.  Enable website options for your bucket, such as index and error document 
4.  Get your endpoint CNAME
(e.g www.foo.bar.s3-website-us-east-1.amazonaws.com) 
5.  Update your DNS record to point to this CNAME 
6.  Upload your web site content to your S3 bucket

Tadam. And it’s even painlessly working…except.

Except some corporate proxy filtering rules seem to not like this setup for some obscure reasons that I can’t get right
for now. Apparently, this is not related to a DNS configuration problem since most people can actually access the
website. It could potentially point out to the actual length of the CNAME which is a bit bigger than usual but according
to the [RFC 1035][6], that shouldn’t be a problem as it stipulate that such CNAME limit should not exceed 253 characters
in dotted form. Moreover, in such case, directly using the amazon CNAME
(e.g. www.foo.bar.s3-website-us-east-1.amazonaws.com) works. Could this be some sort of enforcement to have a CNAME, for
a bare domain (e.g. foo.bar) resolving to the same IP?

 [6]: http://www.apps.ietf.org/rfc/rfc1035.html

I would really appreciate a better explanation for all this but I’m still puzzle.

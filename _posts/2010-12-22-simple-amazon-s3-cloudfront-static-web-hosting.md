---
title: Simple Amazon S3 CloudFront Static Web Hosting
author: Pascal Deschenes
comments: true
layout: post
permalink: /2010/12/simple-amazon-s3-cloudfront-static-web-hosting/
dsq-thread-id:
  - 338446713
categories:
  - howto
tags:
  - aws
  - cloudfront
  - hosting
  - s3
  - web
---

A [recent discussion][1] on the [LinkedIn Amazon Web Services Community Network][2] went on to compare GoDaddy hosting
with Amazon EC2 while discussing about a potential procedure:

 [1]: http://www.linkedin.com/groupItem?view=&srchtype=discussedNews&gid=49531&item=36957117&type=member&trk=EML_anet_qa_ttle-cDhOon0JumNFomgJt7dBpSBA
 [2]: http://www.linkedin.com/groups?mostPopular=&gid=49531

<!-- more -->

> I want to know the clear process flow for hosting a website on AWS. Is it really simple and worth to move our websites
> to AWS? Or web hosting providers like GoDaddy.com are more economical than AWS..?

For my part, I would say that simple web hosting on an Amazon instance is really a piece of cake given that you have
some sysadmin skills and know your way around Linux config files, DNS configuration, and the like. More over, it is dirt
cheap. My last monthly bill was 0,03$

**Pre-Requisites**

*   Amazon [S3][3]/[Cloudfront][4] account with access and secret key pair
*   Domain Name Server (DNS) access

 [3]: http://aws.amazon.com/s3/
 [4]: http://aws.amazon.com/cloudfront/

**Amazon S3 Bucket**

Assuming you have your static content already, the basic idea is to upload your content over S3. More specifically, to a
bucket named after your domain name. For instance, if your domain name is *www.foo.bar*, you should create a bucket
accordingly, that is, *www.foo.bar*. You can either use the Amazon Management Console, an S3 Explorer, or a custom
script. Since I like to automate things, I have crafted a very simple Ruby script based on the [AWS::S3][5] and
[AWS::Cloudfront][6] gems along with some hints provided by [James Murty][7], author of the [Programming Amazon Web
Services][8] book.

 [5]: http://amazon.rubyforge.org/
 [6]: http://rubygems.org/gems/aws-cloudfront
 [7]: http://www.jamesmurty.com/
 [8]: http://www.amazon.ca/Programming-Amazon-Web-Services-SimpleDB/dp/0596515812/ref=sr_1_1?ie=UTF8&qid=1293024203&sr=8-1

**Amazon DNS Delegation**

Once you have your content up in the cloud, you may already access your content at
s*3.amazonaws.com/www.foo.bar/index.html* or *www.foo.bar.s3.amazonaws.com/index.html*. But then, that would not be so
useful. To get your content to appear under your domain requests, you have to delegate those request to Amazon DNS. The
basic idea is really simple: add a new CNAME record for your domain with a *content* to the associated S3 entry. To do
so, simply add the following to your DNS entries:

`www.foo.bar CNAME bucketname.s3.amazonaws.com`

You may now directly access your static content at *www.foo.bar/index.html*

**CloudFront Default Object**

Everything now seems so perfect and straightforward. However, S3 does not quite understand the idea of a default
*index.html*. Hence, if you try to access www.foo.bar directly, you get something like the following:

  <xml version="1.0" encoding="UTF-8"?>
  AccessDenied
  Access Denied770E150FB7E6896C1Wc/H779PlMV v UD72qr9nNA999ASvDZ3azjeBzRAyuxIljrG9icvxBCYEaaVGx

This is unfortunate but using CloudFront, we can work around this problem. The idea is to define a CloudFront
distribution then specify a Default Object. You can define a new distribution straight from the Amazon Management
Console or a custom script. Again, since I like to automate things, I have crafted my own Ruby script on top of the
S3 and CloudFront gems.

Then, you will have to modify the previously defined DNS delegation to use the associated CloudFront.

`www.foo.bar CNAME d1wm1vzff3ch22.cloudfront.net`

Lastly, you need to define your *index.html* as your Default Object. Unfortunately, since this CloudFront feature is
rather [new][9], most interfaces does not yet provide such functionality. So you end up crafting your own custom script
or, again, you might use mine or its web front over my labs site.

 [9]: http://aws.amazon.com/about-aws/whats-new/2010/08/05/cloudfront-adds-default-root-object-capability/

---
title: 'Amazon EC2 Hardware Failure? I don&#8217;t care!'
author: Pascal Deschenes
comments: true
layout: post
permalink: /2011/02/amazon-ec2-hardware-failure-i-dont-care/
dsq-thread-id:
  - 230591283
categories:
  - development
tags:
  - aws
  - ebs
  - ec2
  - snapshot
---

Last Friday, I was trying to reach my EC2 instance. First, my WordPress dashboard, then into my SSH shell account.
Everything felt weird: connection drops, unreachable host, etc. Through the AWS management console, I could execute some
operations while others hanged. Reaaaally odd. Then I started to worry.

<!-- more -->

While I first blamed my DNS, I ended up blaming Amazon’s data center. Looking at the developer Forums, I realized I was
not alone: lots of interruptions were ongoing. Reaching to the AWS [service health dashboard][1] gave me some hints
about some hardware failures which had apparently occurred this very morning. Hum. Then everything was back to normal. I
could reach both my website and shell. Went home early feeling almost assured. A nice weekend in snowy Lanaudiere with
some [friends][2] was unfolding.

 [1]: http://status.aws.amazon.com/
 [2]: http://ax2.ca/

Then, on my way home, I’ve received an email notice straigth from Amazon saying:

![AWS Hardware Failure Notice](/images/aws-notice.png)

How sweet! I’ve never had such outage before so I was not sure whether my snapshot procedure would work out or not (I
know, I should have try it out!). Once home, I fired the AWS Management Console, looked for my last EBS image snapshot,
then launched it. Voilà! A few minutes later, I had my new instance up and running, with its new IP address [dynamically
registered][4] over my DNS. Magic! Got rid of my previous instance. Back to your normal program. We got to love those
cloud infrastructures now.

 [4]: /2010/12/what-about-dynamic-dns-update-behind-a-router/

**Lessons Learned**

1.  [EBS backed instance][5] is a must.
2.  [EBS][6] snapshots are a lifesaver.
3.  Register for a monitoring service such as [pingdom][7] or Montreal-based [Stella][8] to get alerted as soon as
    failures occur.

 [5]: http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/index.html?creating-an-ami-ebs.html
 [6]: http://aws.amazon.com/ebs/
 [7]: http://www.pingdom.com
 [8]: https://www.blamestella.com/

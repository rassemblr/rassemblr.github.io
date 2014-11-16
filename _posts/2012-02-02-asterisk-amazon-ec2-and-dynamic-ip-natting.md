---
title: Asterisk, Amazon EC2, and Dynamic IP NATting
author: Pascal Deschenes
comments: true
layout: post
permalink: /2012/02/asterisk-amazon-ec2-and-dynamic-ip-natting/
Hide OgTags:
  - 0
dsq-thread-id:
  - 562410799
categories:
  - development
  - howto
tags:
  - amazon
  - asterisk
  - dynamic
  - ec2
  - ip
---

When it comes to SIP communications, [NATting][1] always remains an interesting challenge. A SIP client needs to 
instruct the remote server how the RTP audio stream can be routed back once the communication with far end as 
been established.

 [1]: http://en.wikipedia.org/wiki/Network_address_translation

<!-- more -->

Several solutions exist to do so: for a standard SIP client such as a SIP phone, one could use either a [STUN][2] server, 
a SIP [Register][3], or the [ICE][4] technique. Within Asterisk, while support exists for both registration and STUN, 
both alternatives aren’t suitable for high density environment, where the former rely on constant registration requests 
while the latter rely on an external server acting as another point of failure. It is then recommended to use an other 
option. One of such option being the *externhost*, to which one could set a dynamic hostname (i.e. dyndns). However, 
this option is again apparently unsuitable for production environment due to constant DNS resolving requests. Hence 
we are left with either the *externip* parameter or wait for [IPv6][5].

 [2]: http://en.wikipedia.org/wiki/STUN
 [3]: http://tools.ietf.org/html/rfc3261#section-10.2
 [4]: http://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment
 [5]: http://ipv6.com/articles/nat/NAT-In-Depth.htm

*externip*, while perfect for the job, has an important draw back: its value needs to be statically defined, which is against the idea behind having a dynamic ip in the first place. Unless one is using an [Amazon Elastic IP][6], whenever an Amazon Image (AMI) is instantiated, a dynamic public ip is assigned. This dynamic ip can easily be obtained

 [6]: http://aws.amazon.com/articles/1346

    curl -s http://169.254.169.254/latest/meta-data/public-ipv4

In order to circumvent this chicken and egg situation, the idea is to first enable *execincludes* (asterisk.conf) and use the #exec instruction within the Asterisk sip-general.conf general section definition to dynamically obtain the dynamic ip and assign its value to the externip parameters.

    #exec /etc/asterisk/externip.sh
    localnet=10.0.0.0/255.0.0.0

This *externip.sh* script simply output the externip parameter assignation with the dynamic ip:

    #!/bin/bash
    IP=`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`
    echo "externip=$IP"

As a final note, just to make sure the current ip address as been used, one might find useful to reload the sip configuration within the /etc/rc.local boot sequence:

    asterisk -rx 'sip reload'

To validate this new mechanism, simple issue the following to get the actual externip setting and look for the Network Settings section:

    asterisk -rx 'sip show settings'

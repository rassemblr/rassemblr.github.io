---
title: Fighting Asterisk DoS Attack
author: Pascal Deschenes
layout: post
permalink: /2012/05/fighting-asterisk-dos-attack/
Hide OgTags:
  - 0
dsq-thread-id:
  - 693536238
categories:
  - development
  - howto
tags:
  - asterisk
  - attack
  - DoS
  - sip
  - telephony
---

Oh Boy! We have recently experienced a Denial of Service attack on one of our cloud asterisk platform 
hosted on Amazon Ec2. Since our platform does not allow SIP registration under any circumstance, our system hasn’t been compromised.

<!-- more -->

However, we did suffer from registration request overheat: 90K [REGISTER][2] requests within 15 minutes. 
That’s right. I wish I had keep the logs so I could have plot a nice chart but that’s really impressive. 
And that's [DoS][1].

Now, while we didn’t let any peer to register, Asterisk seems to respond to registration request nevertheless. 
On registration attempt, Asterisk will get back with a response basically saying either “invalid peer” or 
“invalid password”. With those assumption in mind, the potential intruder simply performs registration attempts 
against a dictionary (also know as a [dictionary attack][3]) and based on the registration response, move 
on to the next word, or proceed with the password authentication, again using the dictionary.

At some point during the attack, the potential intruder did actually found one of our peer definition 
and started to proceed with password attempts. Again, we don’t allow registration, hence, any password 
would have failed. But it was painful on the CPU nevertheless!

Following a few [blog][4] posts, we decided to go on with the following security mechanism within 
sip.conf. *sip reload* then registration requests stopped immediately afterward.

    allowguest=no
    alwaysauthreject=yes

### Final Note

Yes we did add a rule within our firewall to block that specific attacker IP address but we wanted to 
immune ourself for any other attacker! We might end up setting a [fail2ban][5] rule and implement [some other tricks][6].

 [1]: http://en.wikipedia.org/wiki/Denial-of-service_attack "DoS"
 [2]: http://tools.ietf.org/html/rfc3261#section-10.2 "rfc 3261"
 [3]: http://en.wikipedia.org/wiki/Dictionary_attack "Dictionary Attack Definition"
 [4]: http://jcs.org/notaweblog/2010/04/11/properly_stopping_a_sip_flood/
 [5]: http://www.fail2ban.org/wiki/index.php/Asterisk
 [6]: http://blogs.digium.com/2009/03/28/sip-security/ "Sip Security"

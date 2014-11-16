---
title: Multi VoIP Provider Aggregation and Asterisk Local Channels
author: Pascal Deschenes
layout: post
comments: true
permalink: /2011/12/multi-voip-provider-aggregation-and-asterisk-local-channels/
Hide OgTags:
  - 0
dsq-thread-id:
  - 490390102
categories:
  - development
  - howto
tags:
  - aggregation
  - asterisk
  - local
  - sip
  - voip
---

It’s been a while since I’ve first wanted to try the [Asterisk][1] local channels to offer 
the ability to dial into an aggregation of several VoIP providers so that, from a user 
perspective, the actual provider set is hidden behind the local context. Technically, 
I couldn’t see why it wouldn’t work out but I just did a small experiment today with interesting results.

 [1]: http://www.asterisk.org/

<!-- more -->

Obviously, the idea behind provider aggregation is to scale: leveraging on multiple providers 
to offer a maximum number of concurrent channels. This technique offers a second benefit: 
the ability to initiate calls from several distinct POPs without much hassle.

## Local Channels

From the Asterisk [documentation][2]:

 [2]: http://www.voip-info.org/wiki/view/Asterisk local channels

> “[...] a pseudo-channel [which] loops calls back into the dialplan in a different context.”

Hence, calling a local channel allows one to start the call execution directly within a 
specific execution context. Leveraging on this, provider aggregation is basically hidden 
within such context, where the actual SIP dialing is performed, depending on a mix of variables and conditions.

## Provider Definitions

First, we obviously need to set our providers within the *sip.conf* configuration. Nothing 
special compared to offering direct SIP dialing.

    [provider_1]
    type=peer
    host=192.168.137.56
    dtmfmode=inband
    canreinvite=no
    qualify=1000
    disallow=all
    allow=ulaw
    [provider_2]
    type=peer
    host=192.168.137.56
    dtmfmode=inband
    canreinvite=no
    qualify=1000
    disallow=all
    allow=ulaw

As one can see, while your millage may vary, nothing unexpected. We are just setting the table.

## Local Context

Then within the dial plan, we first need to define a few global variables to be shared 
among the channels.

    globals {
            // configure the limits for each provider
            provider_1_limit=1;
            provider_2_limit=4;
            // no more edit beyond this point
            provider_1_count=0;
            provider_2_count=0;
    };

In this specific case, we have define 2 providers, hence, we are setting the appropriate 
global variables for those, that is, the maximum number of concurrent calls.

Then comes the special sauce.

    context multi-provider {
      _X. => {
        if (${GLOBAL(provider_1_limit)} > ${GLOBAL(provider_1_count)}) {
          Set(PROVIDER_CONTEXT=provider_1);
        }
        else if (${GLOBAL(provider_2_limit)} > ${GLOBAL(provider_2_count)}) {
          Set(PROVIDER_CONTEXT=provider_2);
        }
        else {
          Congestion();
        }
        GLOBAL(${PROVIDER_CONTEXT}_count)=GLOBAL(${PROVIDER_CONTEXT}_count) 1;
        Dial(SIP/${PROVIDER_CONTEXT}/${EXTEN});
        Congestion();
      };
      h => {
        // we could be handling from congestion
        if (${GLOBAL(${PROVIDER_CONTEXT}_count)} > 0) {
          GLOBAL(${PROVIDER_CONTEXT}_count)=GLOBAL(${PROVIDER_CONTEXT}_count)-1;
        }
      }
    }

We define our local context (multi-provider) within which we define 2 extensions handlers, 
one for the actual dialed extension, and the other one for handling the hangup. For each 
provider, we set a condition, comparing the actual count with the limit, then we set 
the *PROVIDER_CONTEXT* channel variable. This variable will not only be used to construct 
the actual *Dial* command, but also within the *h* handler to decrease the count. An *else* 
condition has been used to return with *congestion* if all provider channels are currently in 
used. Finally, we increment the number of channel in use for the selected provider, and 
perform the actual dial on the provider sip channel.

## Dialing In

Now, to use that setup, simply issue the following:

    Dial(local/5141231234@multi-provider/n);

That’s it!

### Areas of Improvements

*   The conditional section is a bit cumbersome (when you have a bunch of providers to setup) 
    and could be improved using a macro.
*   We could probably use some sort of index variable instead of using hard coded variables 
    such as *provider_2*, *provider_2_count*, *provider_2_limit*.
*   Some edge could still be missing at this stage and I might revisit some.
*   Instead of filling the first provider, then the second and so on, we could 
    have perform either a round robin or random selection logic as well.

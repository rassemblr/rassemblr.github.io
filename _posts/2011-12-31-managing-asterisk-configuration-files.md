---
title: A Sane Way to Manage Asterisk Configuration Files
author: Pascal Deschenes
layout: post
permalink: /2011/12/managing-asterisk-configuration-files/
Hide OgTags:
  - 0
dsq-thread-id:
  - 521858198
categories:
  - development
  - howto
tags:
  - asterisk
  - chef
  - configuration
  - telephony
---

How do you manage your [Asterisk][1] configuration files? There are so many of them with so many parameters, 
sections, and such. All too easy to fall into configuration management spaghetti hell.  How do you differentiate 
default v. custom/override? How do you deactivate part of a configuration? This is really driving me [insane in the membrane][2].

<!-- more -->

Some Asterisk [distributions][3] came up with an alternate option using an include file in the form of *sip_custom.conf* 
or even *sip\_general\_custom.conf* for specific section override which is fine for solving part of the problem but far from ideal.

 [1]: http://www.asterisk.org/
 [2]: http://www.youtube.com/watch?v=RijB8wnJCN0
 [3]: http://fonality.com/trixbox/

During this Christmas holidays, I’ve been working on implementing a Chef cookbook for Asterisk (which should be 
the subject of another post). At some point, my main problem was related to properly and cleanly manage configuration 
files. The basic idea involves a few configuration tricks offered within Asterisk configuration mechanism, namely, #include, #exec, and ( ).

### Configuration Layout

Partly inspired by Apache 2 configuration structure from Debian and friendly [a2ensite/a2dissite][4] scripts, I 
came up with the following */etc/asterisk* directory structure:

 [4]: http://manpages.ubuntu.com/manpages/hardy/man8/a2ensite.8.html

![](/images/tree.png)

The basic idea is to move all default configuration files into a *available* directory then enable specific ones on a case 
by case basis, linking the actual config within the *enabled* directory.

### Dynamic Loading

Yes. Yes. But how are the configurations loaded into asterisk now that the initial ones have been moved somewhere else. 
Once again, on a case by case basis, *inclusion* configuration files are created with a simple **[#exec][6]** 
instruction which provides dynamic #include statement. For instance, the following would be the content for sip.conf.

 [6]: https://wiki.asterisk.org/wiki/display/AST/Using The include and exec Constructs

    #exec /usr/bin/asterisk-load sip

where the actual configuration loader is

    #!/bin/bash
    if [ -e /etc/asterisk/enabled/$1.conf ] ; then
            echo "#include /etc/asterisk/enabled/$1.conf";
    fi
    if [ -e /etc/asterisk/enabled/$1/ ] ; then
      find /etc/asterisk/enabled/$1/ -name '*.conf' -exec echo "#include {}" ;
    fi

This script would simply look for *enabled/sip.conf* and `enabled/sip/*/*.conf` files to be dynamically 
loaded if present. Now, to deactivate a specific configuration, associated link within *enabled* 
directory should simply be moved to the *disabled* one.

### Overrides

In order to fulfill all promises, configuration override needs to be taken into account. While any 
configuration files can be enabled/disabled, some configuration key/value within a specific category/section 
may re-defined some from other configuration files, the idea is to rely on another configuration technique, 
namely, **[( )][7]** attributions. For instance, given the following enabled/manager.conf file:

 [7]: https://wiki.asterisk.org/wiki/display/AST/Adding to an existing section

    [general]
    enabled = no
    port = 5038
    bindaddr = 0.0.0.0

we could be interested in activating the manager and define the following enabled/manager/activation.conf

    [general]( )
    enabled = yes

which overrides the key/value has defined in the first configuration file. Sweet.

### Helper

Now to wrap this rather long post, it would be nice to have a script to manage all this wizardry instead of doing 
manual typing which can get confusing. The following [rake file][8] provides a few tasks to handle enablement/disablement.

 [8]: https://gist.github.com/1542978

    RAKEFILE=https://raw.github.com/gist/1542978/fc3f37e4a6417ef130a1cdaa587901c3f9bf005f/Rakefile
    curl $RAKEFILE > Rakefile; rake install
    rake enable[manager]
    rake enable[manager/activation]
    rake disable[manager/activation]

Comments?

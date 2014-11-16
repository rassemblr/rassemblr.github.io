---
title: Managing Distributed Asterisk Configuration and NoSQL Database
author: Pascal Deschenes
layout: post
permalink: /2012/03/managing-distributed-asterisk-configuration-and-nosql-database/
Hide SexyBookmarks:
  - 0
Hide OgTags:
  - 0
dsq-thread-id:
  - 624194900
categories:
  - development
  - howto
tags:
  - asterisk
  - configuration
  - couchdb
  - nosql
---

In my quest of getting out of the configuration spaghetti hell, I’ve previously suggest to use a new 
configuration layout and some helper scripts to load configuration on-demand through #exec instruction. 

<!-- more -->

While [this alternative][1]  is interesting, it lacks in terms of environment distribution where a cluster of 
Asterisk platforms needs to share configuration or at least a central configuration repository. While a 
tiny few DB backed configuration technique exist ([Asterisk RealTime][2] for instance), I feel its time 
to have something new, fresh, and obviously more versatile :-)

 [1]: http://blog.rassemblr.com/2011/12/managing-asterisk-configuration-files/ "A Sane Way to Manage Asterisk Configuration Files"
 [2]: http://www.voip-info.org/wiki/view/Asterisk RealTime

## CouchDB to the rescue

First of, what is couchdb, directly from the [website][3]:

 [3]: http://couchdb.apache.org/docs/intro.html

> *   A document database server, accessible via a RESTful JSON API.
> *   Ad-hoc and schema-free with a flat address space.
> *   Distributed, featuring robust, incremental replication with bi-directional conflict detection and management.
> *   Query-able and index-able, featuring a table oriented reporting engine that uses JavaScript as a query language.

### Benefits

From our perspective, the benefits of this NoSQL DB can be highlighted as follow:

*   Query support from command line through cURL.
*   Automatic replication
*   Revision history
*   Flexible schema

## Components

### Documents

A document is basically a row in SQL terms. Unlike SQL database, documents are schemaless and can contain any 
arbitrary data, which means that a document can be augmented (or new field added) without any (or major) impact. 
Our Asterisk configuration would use the following kind of document:

{% highlight javascript linenos %}
{
  "section": "general",
  "content": {
    "port": "5038",
    "enable": "yes"
  },
  "module": "manager",
  "hostname": "casablanca"
}
{% endhighlight %}

This document defines a hostname, module, section, along with the section content itself as a key-value object hash.

### Views

Views on the other hand are essentially a way to represent the raw data/document through [MapReduce][4] functions, 
against which queries can be performed. In the SQL world, a view could be mapped to a table view. In our 
specific case, the following view (*config*) can be used to get documents based on the configuration module:

{% highlight javascript linenos %}
"views": {
  "config": {
    "map": "function(doc) {
      if (doc.section && doc.module) { 
        emit(doc.module, doc);
      }
    }"
  }
}
{% endhighlight %}

 [4]: http://en.wikipedia.org/wiki/MapReduce

Without going into much details, instead of emitting just the doc.module, we could emit a `[doc.hostname, doc.module]` 
array, allowing us to get the config for a specific cluster host.

### List functions

Now comes the nice part. It’s one thing to get a JSON document out of a view query, but we need output in the 
Asterisk configuration form. [List function][5] allows just that, typically used to return XML or HTML 
version of a given result. It can also be used to return text/plain content type. Great! Here is what we have:

 [5]: http://guide.couchdb.org/draft/transforming.html "CouchDB List Function"

{% highlight javascript linenos %}
"lists": {
      "section": "function(head, req) {
        var row;n
        start({
        'headers': {
          'Content-Type': 'text/html'
         }
        });
        while(row = getRow()) {
          var content = '['   row.value.section   ']\n';
          var items = row.value.content;
          for (var key in items) {
            content  = key '=' items[key]   '\n';
          }
          send(content);
        }
    }"
}
{% endhighlight %}

This list function basically iterates over a view, and creates the section content based on the 
key-value pairs from the *content* object hash.

## PUTting it all together

Then we can PUT our config and `_design` documents to the DB.

{% highlight bash linenos %}
for I in `doc/*.json` ; do
  uuid=`curl 'http://example.com:5984/_uuids'`
  curl -X PUT -v -d @$I 'http://example.com:5984/asterisk/${uuid}'
done
curl -X PUT -v --data-binary @config-design.json \
'http://example.com:5984/asterisk/_design/config'

{% endhighlight %}

Now we can query the list function for a specific hostname and module name through the following:


{% highlight bash linenos %}
curl 'http://example.com:5984/asterisk/_design/\
config/_list/section/config?\
key=["casablanca","manager"]'

[general]
port=5038
enable=yes
{% endhighlight %}

Nice! Now, within the associated manager.conf file, simply perform the #[exec][6] command as follow:

 [6]: https://wiki.asterisk.org/wiki/display/AST/Using The include and exec Constructs


{% highlight bash linenos %}
    #exec load-from-couchdb.sh manager
{% endhighlight %}

Where load-from-couchdb.sh script could be as follow

{% highlight bash linenos %}
#!/bin/sh
hostname=`hostname`
module=$1
curl 'http://example.com:5984/asterisk/_design/config/_list/section/config?key=["{$hostname}","$module"]'
{% endhighlight %}

## More to Come

Using CouchDB and this little list function trick open doors for a few more novelties:

* Config can be easily changed dynamically through any programming language supporting web requests. 
  (Reload still need to be issued through Manager API though).

* A web dashboard could be explored to offer a nice and clean configuration management tool.

* Configuration could be distributed within infrastructure.

* Backup is only a matter of either replicating the config to some other node or copy the database file.

* Authentication is provided out of the box within couchdb itself.

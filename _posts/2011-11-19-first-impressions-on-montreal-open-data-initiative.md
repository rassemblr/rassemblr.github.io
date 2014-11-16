---
title: First Impressions on Montreal Open Data Initiative
author: Pascal Deschenes
layout: post
comments: true
permalink: /2011/11/first-impressions-on-montreal-open-data-initiative/
Hide OgTags:
  - 0
dsq-thread-id:
  - 477004436
categories:
  - development
  - thinking
tags:
  - montreal
  - opendata
---

Montreal [finally][1] [did it][2]! We now have access to some [data set][3] for Montreal. For now, the data 
set are quite limited but reading at the group report ([zip][4]), the effort won’t stop there.

 [1]: http://montrealtechwatch.com/2011/10/28/montreal-open-data/
 [2]: http://spacingmontreal.ca/2011/11/01/montreal-moves-on-open-data/
 [3]: http://donnees.ville.montreal.qc.ca/
 [4]: http://depot.ville.montreal.qc.ca/rapport-ouverture-donnees/data.zip

<!-- more -->

## An Architectural Vision

That being said, while the report provides a vision in terms of business cases – it feels like the 
report was written to convince the authorities about the benefits of opening data – it greatly lack in 
terms of architectural vision. And again, looking at the report, providing this kind of architectural 
vision does not seems to fit within the overall project plan. Bits and bytes here and there but 
not much substance. I truly believe that such architectural vision is core to this kind of project 
where unless developers know where this project is going, they won’t invest time in mashing the data 
set, limiting the crowd sourcing effect.

### Yes Data is the Goldmine, but format is the Ore

While the current data set features broad information from [ski conditions][5] and 
[police station locations][6] to [photographic archives][7], and [socioeconomic statistics][8], 
the format itself won’t help in the crowd sourcing effect. We have currently access to a mix and 
match of data format, where some sets offer a rather standard representation such as XML or CSV, 
while some others offer the information through proprietary Microsoft Excel (.xls) or Autodesk 
Drawing Interchange (DXF) format or even 250Mb of zipped data. I understand that this is 
probably a through-in, to get the first data set out of the door from the city without them 
investing too much. But from a development perspective, it is limited and limiting.

 [5]: http://donnees.ville.montreal.qc.ca/archives/fiche-donnees/conditions-de-ski
 [6]: http://donnees.ville.montreal.qc.ca/archives/fiche-donnees/carte-postes-quartier
 [7]: http://donnees.ville.montreal.qc.ca/archives/fiche-donnees/phototheque-archives
 [8]: http://donnees.ville.montreal.qc.ca/archives/fiche-donnees/annuaire-statistique-agglo

The group needs to standardize on a data set format. Those format needs to be **well established**, 
**well known**, and ideally** open** and from a **standard body** (i.e. [W3C][9], [IETF][10], [ECMA][11], [OASIS][12] …). 
Moreover, parser should be **available for various languages**. [XML][13] obviously come to mind but some 
might argue that [JSON][14] would be an excellent runner up in specific use cases. Those 2 formats should 
become the backbone. Other specific formats should be favored on a case by case basis.

 [9]: http://www.w3.org/
 [10]: http://www.ietf.org/
 [11]: http://www.ecma-international.org/
 [12]: http://www.oasis-open.org/
 [13]: http://www.w3.org/XML/
 [14]: http://www.json.org/

**Normalization**

Moreover, any data set needs some kind of normalization. This is not only how relationship can be built 
around the various kind of data but also, it favours homogeneity and consistency. Normalization is 
defined through rules which should be applied to the data set, transforming the original data into 
something uniform. Typical normalization includes date, time, numbers, amount, toponymy, coordinates, 
photo tags, and such. More specifically for Montreal data, administrative unit and installation 
denominations.

**Relationship**

Finally, relationship. Data set can be mashed into something relevant only if relations exist 
between the data set. As mentioned above, relationship first comes from normalization. However, 
most often, relationship needs to become a by product of any data set to answer questions 
such as "Where are located the ski trails in Complexe environnemental Saint-Michel?" "What 
about the bike counter on Berri?"

## API is the Pickax

Data set are currently available in raw format.

To extract useful information out of this goldmine, we need a good pickax and sluice box! Like we 
have the [Google Map API][15], the [Yahoo Weather API][16], and the [Flickr API][17], Montreal needs its 
very own [Web API][18], like Seattle did with [Socrata][19]. Yes, everyone can start writing there own 
parser. But why not group force and come up with a central API? I like DRO (don’t repeat others!) 
principles. A Web API is not only a web service facade to some back-end data, offered via REST or 
something similar but also usually encompass a set of library in various popular languages. Java, 
Ruby, Javascript, Python, C# comes to mind obviously.

 [15]: http://code.google.com/apis/maps/index.html
 [16]: http://developer.yahoo.com/weather/
 [17]: http://code.flickr.com/
 [18]: http://en.wikipedia.org/wiki/Web_API
 [19]: http://data.seattle.gov/api/docs

But the holy grail would require that data to be organized into specific resource end points or 
URIs, where GET operations can be performed. From a Web API standpoint, a resource is not only the 
actual data file, but also, and that’s where it gets interesting, its underlying semantic.

## Conclusion

Some other aspects could also be addressed in such architecture vision including operation details 
such as domain, status, and updates/notifications (pub/sub) to name a few. Operation is always 
the neglected last mile but that could be an interesting future post on itself.

This is the kind of project that is exciting the for the Montreal developer crowd. While long term 
vision is something that we need to keep in mind, we need to stick with the agile approach an iterate.

Anyone up to the challenge? Ping me on [twitter][20]! I wish I had time to attend the Open 
Data [Hackaton][21] this weekend.

 [20]: http://twitter.com/pdeschen
 [21]: http://montrealouvert.net/2011/10/13/hackathon-donnees-ouvertes-montreal/?lang=en

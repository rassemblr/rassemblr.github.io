---
title: 'Introducing e164.js: country from international phone number'
author: Pascal Deschenes
layout: post
comments: true
summary: I’ve recently release (over GitHub) a small node module (browser javascript friendly) to deal with international phone number and their origination country as specified by the E.164 standard.
permalink: /2012/05/introducing-e164-js-country-from-international-phone-number/
Hide OgTags:
  - 0
dsq-thread-id:
  - 702652209
categories:
  - development
  - howto
tags:
  - country
  - e162
  - e162.js
  - javascript
  - nodejs
  - phonenumber
---

I’ve recently release a small node module (browser javascript friendly) to deal with international 
phone number and their origination country as specified by the E.164 standard.

<!-- more -->

## How it works

[e162.js][1] doesn’t check whether the phone number is a valid one or not. It simply returns the associated country 
based on prefix matching, as per [standard][2]. The matcher starts from the last digit and revert lookup until a 
number matches from the lookup hash.

If a number can’t be found from the lookup hash, number is assumed to be invalid and returns undefined.

## Examples

### Node

Directly from test/test.js

{% gist 2727240 %}

### Browser

Almost directly from test/index.html

      console.log(e164.lookup('15145551234'));

## Installation

### Git Clone

> $ git clone git://github.com/pdeschen/e164.js.git

### Install from npm

> $ sudo npm install e164 [-g]

## Todo

*   return object of the form {country: “”, code: “”}
*   Support for dial out [calling prefix][3] could be offered directly or through options hash

 [1]: https://github.com/pdeschen/e164.js
 [2]: http://en.wikipedia.org/wiki/E.164
 [3]: http://en.wikipedia.org/wiki/International_call_prefix

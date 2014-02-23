---
layout: post
title: 
categories: [ development, ruby ]
comments: true
sharing: true
footer: true
---
When a ruby application runs in a multi-process web container (phusion passenger or puma for example), class variables set in one
instance of the application will not be visible in another instance - so if you use them be very aware that the setting will purely 
apply to the instance of the application in which the variables are set.

If you need to share data across instances of an application, consider using memcache or another host-level data storage mechanism 
with a library that makes it easy to get and set data ( like [Dalli](https://github.com/mperham/dalli) for ruby ).

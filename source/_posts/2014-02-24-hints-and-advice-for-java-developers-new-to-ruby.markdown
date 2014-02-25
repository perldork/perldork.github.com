---
layout: post
title: "Hints and tips for java developers new to ruby"
date: 2014-02-24 22:48:02 -0500
comments: true
categories: [ development, ruby, java ]
---

Most java developers have a really solid grasp of object-oriented
concepts and patterns, which makes the transition to ruby easy really easy
from that perspective.  Moving from a statically typed to dynamic
language is still a big change and some of the more basic coding practices
change significantly.  Here are some recommendations I've come up with
based on my experiences doing mentoring with a bunch of java developers
transitioning to ruby on our team and some code patterns I've heard used
by a colleague of mine who is also mentoring some java developers who
are transitioning to ruby.

### Hints

* Minimize the use of class variables - ruby's class variables have some
  strange inheritence rules, and, with multi-process web containers,
  they don't act as singletons across all instances of an app on the
  same server anyway.
* Prefer local variables over instance variables when possible.
  Minimizing the scope of a variable in a dynamic language reduces the
  possibility of bugs and makes it much easier to see where the variable
  is being set and used.
* Don't pre-initialize data structures - no need.  Arrays and hashes are
  dynamic and in the vast majority of circumstances pre-sizing them does
  not help performance and just leads to more noise in your code.
* Snake-case variables .. thing\_one and thing\_two.
* Semi-colons are not needed at the ends of statements ; only use them
  when doing single-line method definitions or rare cases where you want
  two statements on the same line.
* Prefer composition over complex inheritence trees.
* Prefer mixins over complex inheritence trees.

### Tips

* Explore code with irb or [Pry](http://pryrepl.org/). No faster way to
  get conversant with ruby than to explore using it from a ruby-based
  shell!

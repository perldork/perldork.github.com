---
layout: post
title: "When writing rspec tests 15 pieces of flair is perfect just ignore Brian"
date: 2014-02-27 19:59:49 -0500
comments: true
categories: [ ruby, development, rspec ]
---

{% blockquote Office Space %}
Stan, Chotchkie's Manager: Now, you know it's up to you whether or not
you want to just do the bare minimum. Or... well, like Brian, for
example, has thirty seven pieces of flair, okay. And a terrific smile. 
{% endblockquote %}

Forget Brian, when it comes to writing rspec tests let your lazy come
out.  Do the minimum.  Write a test that just does enough to prove
hat your feature or component behaves as expected.

### Do

* Test all known user story / requirement business paths ( "happy paths" )
  related to the component or feature.
* Write a test that really exercises them with real data ( not mocks
  unless you have no other choice but to use mocks ).
* Write it so it both proves your code does what it is supposed to do
  and so that it teaches other developers looking at it how that part of
  the application or component should behave

### Don't

* Write a placeholder or filler test just so you can say you have test
  coverage
* Validate every single attribute or piece of data related to the test;
  that makes your test very tightly coupled to the implementation and
  means that any tiny change to the code requires the test to be updated
  as well.  This makes developers hate you and your overly high-maintenance tests.
* Be obscure nor clever.  Make the test direct and simple - the way we'd
  all like our English to be!
* Use mocks unless you absolutely need to.  Why?  Mocks quickly become
  lies - if they are flexible enough to take made up method names they are
  likely to continue to take that made up method name even when the real
  implementation changes. Now your test lies to everyone when it says
  it passes because what it tests is something that is not used in the real
  implementation.  The only place I personally feel comfortable with
  mocks is when external network-based services are dependencies and
  having them reliably be available in development and test environments
  is not possible.

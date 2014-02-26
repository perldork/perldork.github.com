---
layout: post
title: "HOW-TO: Rack::Attack with Dalli and Padrino"
date: 2014-02-25 23:24:42 -0500
comments: true
categories: [ development, ruby, padrino, rack ]
---

We use [Dalli](https://github.com/mperham/dalli) for memcached-based route caching with 
[Padrino](http://www.padrinorb.com/) 0.12. I wanted to add rate throttling for all /api
route handlers to our app and wanted to use Dalli for that as well.

I found [Rack::Attack](https://github.com/kickstarter/rack-attack), a
really nice Rack-based gem that allows for connection throttling,
blacklisting and whitelisting of clients.  Cool stuff, perfect for our
needs.  Except it expects the caching layer to conform to the
ActiveSupport::Cache::Store interface:

{% blockquote %}
Note that Rack::Attack.cache is only used for throttling; not
blacklisting &amp; whitelisting. Your cache store must implement increment
and write like ActiveSupport::Cache::Store.
{% endblockquote %}

Darn!  Oh, wait, not so bad.  But I don't want to have to do inline
creation of the client etc in app/app.rb, especially because I'm likely
to have different throttling requirements for different environments.

### Modules and blocks to the rescue!

1. Create a subclass for the Dalli client that conforms to the ActiveSupport API.
1. Create a helper method that can be used inline in app/app.rb that
   takes a block with an environment-specific policy.
1. Wrap the use and cache client creation so it doesn't have to be
   repeated in app/app.rb


##### Our security mixin module with configure_security helper

{% codeblock lang:ruby lib/security.rb %}

module Security

  require 'dalli/client'

  @@store = nil

  # Dalli client does not conform to cache client
  # interface Rack::Attack expects - so add wrappers
  # for methods.

  class MemStore < Dalli::Client
    def decrement( key, step, options )
      decr( key, step, options[ :expires_in ] )
    end
    def increment( key, step, options )
      incr( key, step, options[ :expires_in ], nil )
    end
    def write( key, value, options )
      # Have to use raw => true for write or we get marshall
      # errors as Dalli marshals the Fixnum object instead of
      # sending the raw integer value
      set( key, value, options[ :expires_in ], :raw => true )
    end
  end

  def initialize_store
    if @@store.nil?
      use Rack::Attack
      @@store = Rack::Attack.cache.store = MemStore.new %{localhost:11211}
    end
  end

  # Called from app/app.rb
  def configure_security
    initialize_store
    yield( Rack::Attack )
  end

end

{% endcodeblock %}

##### Now use it in app/app.rb

{% codeblock lang:ruby app/app.rb %}

    self.extend Security # lib/security.rb - adds configure_security method

    configure :demo, :development, :api do

      # Rate limit calls to /api URLs to 2 a second in all envs.
      configure_security do |client|
        client.throttle('req/ip', :limit => 2, :period => 1.second) do |req|
          # If we return false nothing is cached ( no rate limiting )
          # - only rate limit calls to /api URLs
          req.path.index( %{/api} ).nil? ? false : req.ip
        end
      end

    end
{% endcodeblock %}



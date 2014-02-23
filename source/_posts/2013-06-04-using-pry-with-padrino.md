---
layout: post
title: Using pry with Padrino
date: 2012-06-04
categories: [ development, padrino, ruby ]
comments: true
sharing: true
footer: true
---
Pry is a really powerful replacement for IRB – I love using it with Rails. I recently started working with Padrino and of course had to have it working there as well.

Pry will read and execute a .pryrc file that exists in the current directory as it boots – here is my Padrino specific .pryrc file – it boots your app just like you’d get in Rails using Pry:


{% codeblock lang:ruby .pryrc %}
load %{./config/boot.rb}
load %{./config/database.rb}
load %{./config/apps.rb}

require 'padrino-core/cli/console'
require 'padrino-core/cli/adapter'
require 'padrino-core/cli/base'
require 'padrino-core/cli/rake'
require 'padrino-core/cli/rake_tasks'

def app
  Padrino.application
end

def start( server = :puma )
  puts %{Type Ctrl-C to background server}
  Padrino.run! server: server
rescue Exception => e
  puts %{Problem starting server: #{ e.to_s }}
end

def stop
  puts %{Stopping server}
  threads = Thread.list
  threads.shift
  threads.each { |t| Thread.kill t }
end

def save_to( file, contents )
  File.open( file, %{w} ) { |f| f.puts contents }
end
{% endcodeblock %}

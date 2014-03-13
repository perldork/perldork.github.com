---
layout: post
title: "Testing Log Messages with Rspec and Padrino"
date: 2014-03-13 14:41:08 -0400
comments: true
categories: [ruby,padrino,rspec]
---
.
{% blockquote Office Space %}
And if you could could go ahead and get a can of psticide and take care 
of the roach problem we've been having that would be great.
{% endblockquote %}

While as a general rule testing log output can make tests brittle
because logs are free text and can change easily, there are certainly
cases where testing them makes sense.

In my case our application was missing some really important business
flow logging that we discovered was needed when we attempted to
troubleshoot a production issue. We had to spend a significant amount of
time hunting something down that would have been trivial to find in a
well formatted info log message. I added that logging to the
application and then wanted to make sure these business level log messages
were being emitted at the right places in the application.

To test log messages in Padrino, I added the following helper to our
spec/support/spec_helpers.rb file:

{% codeblock lang:ruby spec/helpers/spec_helpers.rb %}

# Pass a block and log output will be captured in that block and
# returned as an array of lines for testing.
def capture_logger_output
  logger.flush
  # This causes messages to be captured in the internal logger array
  # that can be accessed via logger.buffer.
  logger.auto_flush = false

  # Run user's code
  yield

  # Save the log lines captured during the test block
  # NOTE: have to clone otherwise flush clears the referred to array
  buffer = logger.buffer.clone

  logger.flush
  logger.auto_flush = true

  buffer
end

{% endcodeblock %}

Now in your tests you can run test code and capture any log messages that
happened during the code run!

{% codeblock lang:ruby spec/helpers/spec_helpers.rb %}

it &quot;should test something&quot; do
  messages = capture_logger_output do
    post %{/to/some/url}, params, set_csrf
  end
  expect( last_response.status ).to eq 200
  expect( messages.select{ |line| line =~ /general match/ }.first ).to match /specific-pattern/
end

{% endcodeblock %}

Happy log testing!


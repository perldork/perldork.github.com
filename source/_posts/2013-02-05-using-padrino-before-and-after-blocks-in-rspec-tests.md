---
layout: post
categories: [ development, padrino, ruby ]
date: 2012-02-05
comments: true
sharing: true
footer: true
---
We add in code that allows us to send mock parameters to controller actions via Capybara tests for all controllers while testing -so we can simulate session state ( for example - user being logged in ). In Rails you do this by re-opening ApplicationController in spec/spec_helper.rb and adding in a before_filter. In Padrino you can do this by adding custom code to app.rb in a before block - the before block is called for every controller action.

{% codeblock lang:ruby app/app.rb %}
configure :test do
  before do
    params.keys.each do |param|
      if param =~ /^mock_/
        mock_param = param.gsub(/mock_/, '')
        session[ mock_param ] = params[ param ]
        logger.debug %{ #{mock_param} set to #{params[ param ]}} 
      end
    end
  end
end
{% endcodeblock %}

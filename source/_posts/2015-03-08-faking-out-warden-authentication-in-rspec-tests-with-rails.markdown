---
layout: post
title: "Faking out Warden authentication in rspec tests with Rails"
date: 2015-03-08 18:55:26 -0400
comments: true
categories: [ development, rails, ruby ]
---

{% blockquote Office Space %}
Bob Porter: he [Milton] was laid off five years ago and no one ever told him about it; but through some kind of glitch in the payroll department, he still gets a paycheck.
Bob Slydell: So we just went ahead and fixed the glitch.
{% endblockquote %}

Using real authentication for every controller or request test written
with rspec / Capybara is a pain. We recently worked out how to fake out
Warden authentication in Rails by monkey patching the Rails base
application controller in the test environment to have a before filter
that strips out the 'mock\_user\_id' parameter from the params hash and
then uses that to look up the user object from our database and re-create
the session variables Warden expects to see to validate that a user is logged in.

{% codeblock lang:ruby spec/support/helpers/application_controller_monkeypatch.rb %}
# this controller will only be used for tests to
# populate a session with mock parameters passed from tests
class ApplicationController < ActionController::Base
  before_filter :init_session

  def init_session

    # Special code to fake out Warden authentication
    if params['mock_user_id'].present?
      user = User.find_by_id(params['mock_user_id'])
      session['warden.user.user.key'] = [[user.id], user.encrypted_password[0..28]]
      session['email'] = user.email
      params.delete 'mock_user_id'
    end

  end
end
{% endcodeblock %}

Then, in your test:

{% codeblock lang:ruby spec/request/a_cool_test.rb %}
describe 'test block' do

  let!(:user) { create(:user) }

  it 'has a test description' do
    get '/user/?mock_user_id=' + user.id.to_s
    expect(response.status).to eq 200
    # [...]
  end
end
{% endcodeblock %}

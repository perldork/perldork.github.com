---
layout: post
title: "Faking out Warden authentication in rspec tests with Rails"
date: 2015-03-08 18:55:26 -0400
comments: true
categories: [ development, rails, ruby ]
---

Using real authentication for every controller or request test written
with rspec / Capybara is a pain. We recently worked out how to fake out
Warden authentication in Rails by allowing the user to pass in
'mock\_user\_id' as an HTTP parameter to URLs and we then monkey path in
a before method in our base controller in tests (only) that fakes out
Warden authentication so we don't have to do real logins for each and
every test.

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

---
title:  "NO_PROXY monkey patch for Faraday"
date:   2015-08-20 20:00:00
layout: post
categories: [ruby]
---

At work we've been using [Faraday][faraday] as our main HTTP library. We have four application that communicate with each other ussing HTTP. The architecture is a little bit complex and some of the hosts have acccess to the outside world and some don't. Because of that, sometimes we have to ignore the proxy and go directly from one host to another, using its VIP...
<!--more-->

For instance, Faraday does not support a way of avoiding proxy or setting up a proxy exception. Check it out [here][issue_faraday] and [here][issue_faraday_2].

Luckly we're able to workaround this problem with a simple solution that works like a charm for us.

We use [chef][chef] to deal with our infrastructure and also with our environment variables, so all you have to do is export your ENV['no_proxy'] variable, something like this:

{% highlight ruby %}
# Set Rails environment for this environment.
<% if node.chef_environment == "prod" %>
  RAILS_ENV=production
  http_proxy="http://px.fake.host:3128"
  https_proxy="http://px.fake.host:3128"
  no_proxy="127.0.0.1,localhost,vip-application-correios.fake.host"
<% elsif node.chef_environment == "stg" %>
  RAILS_ENV=staging
  http_proxy="http://proxy.qa.fake.host:3128"
  https_proxy="http://proxy.qa.fake.host:3128"
  no_proxy="127.0.0.1,localhost,fake.host,s3.qa.fake.host"
<% end %>
{% endhighlight %}

Ok, we're ready to go with the monkey patch. In order to send HTTP requests from one application to another, we have a simple gem with objects mapped with [her][her], so before initializing your gem, all you have to do is:

{% highlight ruby %}
class Faraday::Connection
  def proxy(arg = nil)
    @proxy = nil
  end
end
{% endhighlight %}

And then, setup your gem with Faraday classes:

{% highlight ruby %}
Her::API.setup options do |c|
  c.use ::Faraday::Request::BasicAuthentication, usr, passwd
  c.use ::Faraday::Request::UrlEncoded
  c.use ::Faraday::Adapter::NetHttp
end
{% endhighlight %}

Pretty simple, huh?

[faraday]: https://github.com/lostisland/faraday
[issue_faraday]: https://github.com/lostisland/faraday/pull/471
[issue_faraday_2]: https://github.com/lostisland/faraday/pull/247
[chef]: https://www.chef.io/chef/
[her]: https://github.com/remiprev/her

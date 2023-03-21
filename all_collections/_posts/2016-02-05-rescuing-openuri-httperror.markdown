---
title:  "Rescuing OpenURI::HTTPError with a specific code"
date:   2016-02-05 08:00:00
layout: post
categories: [ruby]
---

This week at work we struggled to find a nice way to rescue an exception (OpenURI::HTTPError) in a way that doesn't look terrible. Digging into the internet, there wasn't much information so I decided to post our solution here, it may be not the perfect one, but it was the best we could get...
<!--more-->

In our context, from time to time, we go to a url and check if it's already available, otherwise we get a 404, which is perfectly expected in our case. To avoid NewRelic to warn us every single time with this error, we decided to catch only the 404 error and try again (it's a Sidekiq job) until a certain amount of times.

{% highlight ruby %}
require 'open-uri'

def check_if_url_is_available
  url = open('http://your-web-site.com/file.txt')
rescue OpenURI::HTTPError => e
  raise FileNotReady if e.io.status[0] == 404
  raise e
end

class FileNotReady < StandardError; end
{% endhighlight %}

In this way, we're able to add this class __FileNotReady__ as an exception for NewRelic.

If you go the the `OpenURI::HTTPError` [documentation][httperror] you can see that there are two arguments: __message__ and __io__, the last one is the one we need to check the code. In our case, __message__ would be _404 Not found_.


[httperror]: http://ruby-doc.org/stdlib-2.1.0/libdoc/open-uri/rdoc/OpenURI/HTTPError.html

## How can we test this?

{% highlight ruby %}
require 'spec_helper'

describe FooClass
  context '#check_if_url_is_available' do
    context 'raising 404 Not found' do
      before do
        io = double(:io, status: [404, 'Not found'])

        allow_any_instance_of(described_class).to
          receive(:open).and_raise(
            OpenURI::HTTPError.new('404 Not found', io)
          )
      end
    end

    it { expect { subject }.to raise_error(FileNotReady) }
  end
end
{% endhighlight %}

Hope this can help you! Cheers.

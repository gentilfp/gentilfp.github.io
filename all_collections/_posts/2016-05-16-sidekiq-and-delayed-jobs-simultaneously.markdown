---
title:  "Using Sidekiq and DelayedJob simultaneously"
date:   2016-05-16 08:00:00
layout: post
categories: [ruby]
---

I'm working on a project at the moment in which DelayedJob was the one responsible for doing all the background job. For a new feature, we have decided to use Sidekiq instead...
<!--more-->

It was going well until I remembered that both of them implements the method `delay`.

It is known that DelayedJob stores the job into a MySQL database (in our case) and process it later. On the other hand, Sidekiq uses redis for handling the jobs.

It is our intention to move all the backgound work to Sidekiq one day, but that's a whole another story.

Just to mention, in order not to worry about the `delay` method in DelayedJob, we can setup like this:

{% highlight ruby %}
Delayed::Worker.delay_jobs = false
{% endhighlight %}

This will ignore everytime you call a method through `delay` and your specs are good to go.

{% highlight ruby %}
my_object.delay.my_method
{% endhighlight %}

If you prefer, you can disable it only for the test environment.

{% highlight ruby %}
Delayed::Worker.delay_jobs = !Rails.env.test?
{% endhighlight %}

After adding Sidekiq gem to Gemfile I realized that a huge amount of tests broke!

Fortunately, we can _disable_ the `delay` method in order not to conflict with the ones implemented by DelayedJob.

{% highlight ruby %}
Sidekiq.remove_delay
{% endhighlight %}

This will disable all the `delay_` method that Sidekiq implements:

{% highlight shell %}
- delay
- delay_for
- delay_until
{% endhighlight %}

If you still want them to be used by both Sidekiq and DelayedJob, you can still make it happen.

{% highlight ruby %}
Sidekiq.hook_rails
{% endhighlight %}

This will give you the opportunity to use them as:

{% highlight shell %}
- sidekiq_delay
- sidekiq_delay_for
- sidekiq_delay_until
{% endhighlight %}

You can read more about it [here][sidekiq-doc]

Cheers.

[sidekiq-doc]:https://github.com/mperham/sidekiq/wiki/Delayed-extensions#disabling-extensions

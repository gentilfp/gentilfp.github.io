---
title:  "Install pg gem on MacOS"
date:   2017-03-21 18:00:00
layout: post
categories: [ruby]
---

If you're facing an error while installing like this one..
<!--more-->

{% highlight shell %}
Building native extensions. This could take a while... ERROR: Error installing pg: ERROR: Failed to build gem native extension.
{% endhighlight %}

Try downloading [Postgress.app][pgapp] and then install the gem this way:

{% highlight shell %}
gem install pg -- --with-pg-config=/Applications/Postgres.app/Contents/Versions/9.6/bin/pg_config
{% endhighlight %}

PS. Remember to specify Postgres.app version above.

Cheers :)

[pgapp]: https://postgresapp.com/

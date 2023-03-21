---
title:  "Stop and remove Docker containers"
date:   2017-03-13 18:00:00
layout: post
categories: [docker]
---

Quickest way to stop and remove your Docker containers all at once...
<!--more-->

You're probably familiar that

{% highlight shell %}
$ docker ps
{% endhighlight %}

will list your containers. With

{% highlight shell %}
$ docker ps -q
{% endhighlight %}

you have only their container ids.

In this way, a simple way to stop your running containers is

{% highlight shell %}
$ docker stop $(docker ps -q)
{% endhighlight %}

You can also include in this list your already-exited containers by running

{% highlight shell %}
$ docker stop $(docker ps -qa)
{% endhighlight %}

In order to remove them, all you have to run is

{% highlight shell %}
$ docker rm $(docker ps -qa)
{% endhighlight %}

Cheers :)

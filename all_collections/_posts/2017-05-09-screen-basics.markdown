---
title:  "Screen basic cheat sheet"
date:   2017-05-09 18:00:00
layout: post
categories: [tools]
---

"[Screen][screen] is a full-screen window manager that multiplexes a physical terminal between several processes"
<!--more-->

Here are listed a few useful screen commands:

* Create a named session
{% highlight shell %}
$ screen -S NAME_OF_SESSION
{% endhighlight %}

* Detach an active session (inside)
{% highlight shell %}
C-a d
{% endhighlight %}

* Detach and logout an active session (inside)
{% highlight shell %}
C-a D D
{% endhighlight %}

* Detach a running session
{% highlight shell %}
screen -d NAME_OF_SESSION
{% endhighlight %}

* List sessions
{% highlight shell %}
$ screen -ls
{% endhighlight %}

This post tends to grow up! :)

[screen]: https://www.gnu.org/software/screen/manual/screen.html

---
layout: post
title:  "Dual monitor with XRAND and Ubuntu 10.10"
date:   2011-01-16 20:00:00
categories: [linux]
---

Well, I had (twice) troubles with the combination of a Full HD monitor, my Intel 945GM video card and Ubuntu 10.10. Everybody knows that there’s something unfinished when you want to configure them. So, I’m going to give you this brief tutorial explaing how to configure it without any kind of mess...

<!--more-->

First of all, if you have any idea what I’m talking about, but you already know that you cannot configure your dual, or even single monitor via VGA or HDMI you should take a look at [Xrand Manual][xrand] and try to understand how it works.

If you have a small idea about how Xrand works, but you don’t have a clue to solve your problem, let’s start it by understanding how we are going to fix it.

When I plugged my monitor and tried to set it up, Ubuntu even recognized the real resolution, which is 1920×1080 and the right frequency of 60Hz, but for some random reason, my monitor screen was flickering and I was not able to see anything.

So, if you have the same problem that I had, you should follow the next steps, one by one.

Go to your terminal and _sudo su_ on it. After that, type _xrandr_ to make sure that your monitor is plugged in and recognized by Ubuntu.

If everything went well until here, next lines are easy, just copy and paste it.

Now, we should create a new mode line for your xrandr.

{% highlight shell %}
xrandr –newmode "X" 195 1920 2040 2248 2576 1080 1081 1084 1118 -HSync +Vsync
{% endhighlight %}

Later, add the mode line earlier created.

{% highlight shell %}
xrandr –addmode VGA1 "X"
{% endhighlight %}

Select your output with this mode

{% highlight shell %}
xrandr –output VGA1 –mode "X"
{% endhighlight %}

Finally, this is optional, but if you’d like to use your Full HD monitor as the primary one, follow the next line and hit ENTER.

{% highlight shell %}
xrandr –output VGA1 –primary
{% endhighlight %}

Well, that’s it! Pretty simple, huh?

It took me a few months to figure out why I was not able to set it up. If this mode line does NOT fix your problem, let me know in the comment area bellow, I’ll try to help you.

PS. Since Ubuntu 10.04 or later have no xorg.conf file, keep this code with you. In my case, there’s no need to execute every time, because it’s not always that I have my laptop and my monitor together, but if that’s the case for you, you can create a script and run it every time your Ubuntu goes on.

[xrand]: https://www.x.org/archive/X11R7.5/doc/man/man1/xrandr.1.html

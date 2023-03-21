---
title:  "Opening Sublime Text in fullscreen mode"
date:   2016-05-18 08:00:00
layout: post
categories: [tools]
---

This is just a quick tip for those who use Sublime Text in fullscreen mode. I've realized that my default workflow is always..
<!--more-->

{% highlight shell %}
1) $ cd project/i/am/working/on
2) $ git pull
3) $ subl .
4) CMD + SHIFT + ENTER
{% endhighlight %}

I checked and couldn't find a way to setup in _Preferences: Settings - User_ to make that 4th step
automatically.

Luckly, SublimeText accepts a parameter when opening with

{% highlight shell %}
$ subl
{% endhighlight %}

All you have to do is

{% highlight shell %}
$ subl --command toggle_full_screen
{% endhighlight %}

If you're lazy like me and simply can't remember all those more-than-ten-characters-command-line, you
can always create an alias for that :)

Cheers.

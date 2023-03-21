---
title:  "My development environment"
date:   2015-08-04 20:00:00
layout: post
categories: [tools]
---

Since this is the first post here after 2 years without blogging, I've decided to start with my 24/7 development environment...
<!--more-->

VIM / Sublime Text
==================

I've always been a huge fan of Sublime Text and its immensity of plugins, but as a web developer I was constantly being forced to ssh to different machines and I decided it was a waste of time not learning vim! I realized that after you start using, you learn one or two commands per day and in while you're a vim ninja! I'm still far from a ninja but I can take care of myself without a GUI. Nowadays I use both! WAT? Yes, both!

* My whole vim setup can be found [here][vimrc]
* And here is a list of my Sublime Text 3 plugins!
{% highlight shell %}
BeautifyRuby         # github.com/CraigWilliams/BeautifyRuby
BracketHighlighter   # facelessuser/BracketHighlighter
Cucumber             # drewda/cucumber-sublime-bundle
GitGutter            # jisaacks/GitGutter
JsFormat             # jdc0589/JsFormat
Monokai Extended     # jonschlinkert/sublime-monokai-extended
Sass                 # nathos/sass-textmate-bundle
SideBarEnhancements  # titoBouzout/SideBarEnhancements
SyncedSideBar        # sobstel/SyncedSideBar
Vintageou            # guillermooo/Vintageous

# Yes, I use vim "inside" Sublime Text ;)
{% endhighlight %}

OS
==
I've been using MacOS since Mountain Lion when I bought my first macbook pro in 2012. Since 2014 I've been using [Yosemite][yosemite], it has its ups and downs but I like it.

Terminal
========
As every (?) mac user, [iTerm2][iterm2] as the default-24/7-never-close terminal. I <3 [zsh][zsh] and get all the stuff setup with [oh-my-zsh][oh-my-zsh].

![zsh](/img/posts/environment/zsh.png)

Communication
=============
Sometimes skype is needed and so is hangout! But seriously, how was communication before [Slack][slack]?

{% highlight shell %}
# http://www.rubyonrails.link/
{% endhighlight %}

Browsing
========
I've been using Chrome since the beginning, I guess. But sometimes I just get tired of its no-left-ram-for-me behaviour and switch to Firefox. Also I like to have it installed in different languages to test locales, Firefox is in English, Firefox Developer Edition is in Portuguese and Chrome in german.

RVM
===
I can't say if [rvm][rvm] is better/faster/easier than [rbenv][rbenv], but I've been using since I started coding in Ruby, it has always worked for me so I don't see a reason to change, I do believe both do their jobs very well.

{% highlight shell %}
rvm rubies
   ruby-1.9.3-p551 [ x86_64 ]
   ruby-2.0.0-p576 [ x86_64 ]
   ruby-2.0.0-p598 [ x86_64 ]
   ruby-2.1.0 [ x86_64 ]
   ruby-2.1.2 [ x86_64 ]
   ruby-2.1.3 [ x86_64 ]
   ruby-2.1.4 [ x86_64 ]
   ruby-2.1.5 [ x86_64 ]
   ruby-2.1.6 [ x86_64 ]
   ruby-2.2.0 [ x86_64 ]
   ruby-2.2.0-preview1 [ x86_64 ]
   ruby-2.2.1 [ x86_64 ]
=* ruby-2.2.2 [ x86_64 ]
{% endhighlight %}



Just a quick list of my favorite app as well, it's not directly related to development, but I can't live without them!
{% highlight shell %}
Evernote      Wunderlist      Spotify
Mailbox       Feedly          Pocket
{% endhighlight %}


[vimrc]: https://github.com/fpgentil/vimrc
[yosemite]: https://www.apple.com/osx/
[iterm2]: https://www.iterm2.com/
[zsh]: http://www.zsh.org/
[oh-my-zsh]: http://ohmyz.sh/
[slack]: https://slack.com/
[rvm]: https://rvm.io/
[rbenv]: http://rbenv.org/

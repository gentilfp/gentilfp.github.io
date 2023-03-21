---
title:  "Installing nokogiri painlessly"
date:   2016-02-02 22:00:00
layout: post
categories: [ruby]
---

If you're a rails developer, I'm pretty sure you have already found this image below...
<!--more-->

![zsh](/img/posts/nokogiri/nokogiri_fail.png)

Most of the cases, that happens because nokogiri depends on libxml2 and it's probably not finding it. There are thousand options that you can give before installing like this list:

{% highlight shell %}
Provided configuration options:
  --with-opt-dir
  --without-opt-dir
  --with-opt-include
  --without-opt-include=${opt-dir}/include
  --with-opt-lib
  --without-opt-lib=${opt-dir}/lib
  --with-make-prog
  --without-make-prog
  --srcdir=.
  --curdir
  --ruby=${rub-dir}
  --help
  --clean
  --use-system-libraries
  --enable-static
  --disable-static
  --with-zlib-dir
  --without-zlib-dir
  --with-zlib-include
  --without-zlib-include=${zlib-dir}/include
  --with-zlib-lib
  --without-zlib-lib=${zlib-dir}/lib
  --enable-cross-build
  --disable-cross-build
  --with-xml2lib
  --without-xml2lib
  --with-libxml2lib
  --without-libxml2lib
{% endhighlight %}

Here is what is going to save your life :-) That usually works for me!

{% highlight shell %}
gem install nokogiri -- --with-xml2-include=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.10.sdk/usr/include/libxml2 --use-system-libraries
{% endhighlight %}

If it still does not work, you can try a few different things - assuming you're on a mac.

{% highlight shell %}
$ brew link libxml2 libxslt
$ brew link libxml2 libxslt --force
$ gem install nokogiri -- --with-xml2-include=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.10.sdk/usr/include/libxml2 --use-system-libraries
{% endhighlight %}

And hope to see...

{% highlight shell %}
This could take a while...
Successfully installed nokogiri-1.6.6.4
1 gem installed
{% endhighlight %}

PS. Remember that you can always pass the exact version you're a looking for with `-v '1.6.6.4'`

I hope that helps. Cheers!

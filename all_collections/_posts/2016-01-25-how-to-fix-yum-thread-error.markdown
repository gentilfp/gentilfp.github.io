---
title:  "'YUM: Thread died in Berkeley DB library'"
date:   2016-01-25 22:00:00
layout: post
categories: [linux]
---

At work I've been using rpm packages to deploy the application since forever. Sometimes, for an unknown reason (perhaps because all the operations are done remotely with the CI), all the operations with yum stop working and all I get is...
<!--more-->

{% highlight shell %}
[root@localhost RPMS]# yum update
rpmdb: Thread/process 7977/139764163434432 failed: Thread died in Berkeley DB library
error: db3 error(-30974) from dbenv->failchk: DB_RUNRECOVERY: Fatal error, run database recovery
error: cannot open Packages index using db3 –  (-30974)
error: cannot open Packages database in /var/lib/rpm
CRITICAL:yum.main:

Error: rpmdb open failed
{% endhighlight %}

There is an easy solution that I have to Google every single time because I don't remember, that's the reason I decided to blog it here :)
Simply run this code and things should get back to normality, otherwise, please let me know and I can update this post.

{% highlight shell %}
[root@localhost RPMS]# rm /var/lib/rpm/__db*
{% endhighlight %}

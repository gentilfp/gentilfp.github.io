---
layout: post
title:  "Fix your non-bootable linux partition"
date:   2011-03-05 20:00:00
categories: [linux]
---

I have a dual boot laptop, Ubuntu 10.10 which I use the most of the time, rails and C++, and Windows 7, the one that I live most to develop in .NET. It happened twice with me, and I still don’t know why, sometimes I just turn my computer on and Ubuntu cannot be mounted! Grub seems to be working correctly, so is Windows, although Ubuntu does not work with neither of my kernel versions...

<!--more-->

It keeps sending me a message:

{% highlight shell %}
mount: mounting /dev/disk/by-uuid/758efa4f-7500-4143-8b50-b1d0af4cdeaa on /root
failed: Invalid argument
mount: mounting /dev on /root/dev failed: no such file or directory
mount: mounting /sys on /root/sys failed: no such file or directory
mount: mounting /proc on /root/proc failed: no such file or directory
Target filesystem doesn't have /sbin/init.
No init found. Try passing init= bootarg.

BusyBox v1.13.3 (Ubuntu 1:1.13.3-1ubuntu11) built-in shell (ash)
Enter 'help' for a list of built-in commands.

(initramfs)
{% endhighlight %}

Pretty weird, right?

Well, so I found over the internet a simple way to fix it, and since I did it, my computer is working swell, both OS.

So, the first thing you will need is a live Ubuntu bootable, it can be a USB drive or a CD. In my case I always have a live CD with me, in case that this kind of trouble shows up.

First step you got to do is boot you Ubuntu via live CD or USB drive.

When your Ubuntu is running, open you terminal and type:

{% highlight shell %}
$ sudo fdisk -l
{% endhighlight %}

And then you should get something like:

{% highlight shell %}
Disk /dev/sda: 160.0 GB, 160041885696 bytes
255 heads, 63 sectors/track, 19457 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x7a92d565

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1        5099    40957686    7  HPFS/NTFS
/dev/sda2            5100       19456   115322369    f  W95 Ext'd (LBA)
/dev/sda5            7650       19456    94839696    7  HPFS/NTFS
/dev/sda6            5100        5349     1999872   82  Linux swap / Solaris
/dev/sda7            5349        6564     9764864   83  Linux
/dev/sda8            6564        7649     8715264   83  Linux

Partition table entries are not in disk order
{% endhighlight %}

Well, now you already know which partition cannot be mounted. In my case, it’s the /dev/sda7 one.

You can also use the command below to check the UUID that has not being mounted:

{% highlight shell %}
$ sudo blkid
{% endhighlight %}

And get something like:

{% highlight shell %}
/dev/sda1: UUID="4A30F40E30F3FEAF" TYPE="ntfs"
/dev/sda5: UUID="6A987DB9987D847B" TYPE="ntfs"
/dev/sda6: UUID="6072f9ab-7d4f-4ef9-937d-2e22cdb1cd91" TYPE="swap"
/dev/sda7: UUID="758efa4f-7500-4143-8b50-b1d0af4cdeaa" TYPE="ext4"
{% endhighlight %}

It means I have 2 NTFS partitions, one is where my Windows 7 is installed, the second one I use for saving data, and then 3 partitions where my Ubuntu lives, swap, home and root.

Very important: Here you can see that the UUID matches with the one that gave you the error before.

Now that we know everything we need to fix the problem, here it goes the magic. There’s just one command you need to run to fix the partition and make it bootable again.

{% highlight shell %}
$ sudo fsck /dev/sda7
{% endhighlight %}

PS. If you are the one that like to know everything, you can check the documentation of ‘fsck’ [here][fsck].

It will ask you a few times to confirm, and that will fix your partition. After that, reboot your computer normally, take the Ubuntu live CD or USB drive out and start your Ubuntu from your HD.

That should fix the problem.
Well, that’s it! Pretty simple, huh? Also very important to get you out of this unconfortable situation.

[fsck]: http://docs.freebsd.org/44doc/smm/03.fsck/paper.pdf

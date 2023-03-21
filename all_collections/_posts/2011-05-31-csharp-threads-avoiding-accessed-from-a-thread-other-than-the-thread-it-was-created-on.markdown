---
title:  "C# Threads - Avoiding 'Accessed from a thread other than the thread it was created on'"
date:   2011-05-31 20:00:00
layout: post
categories: [c#]
---

I’ve been busy and had no time for a new post, but I got into a curious situation two weeks ago, that was the first time I actually needed to work with different threads in C#, in this case, my application would not be busy while processing. I know there are a lot of tutorials on the internet explaining about threads, so I decided to build an small application using Treads, Reflection and Delegate, so it can be used in your app also...
<!--more-->

First of all, you know understand a little bit of those important concepts very common in C#. So, I recommend that you take a look at those web sites:

* [Threads][threads]
* [Reflection][reflection]
* [Delegate][delegate]

Well, since you already understand their purpose, let’s work on our code.

What will it be? I’ll build a WindowsForm application and work with two threads, one will be responsible for modifing the value of a label, and the second one will be our app, thus, we will be able to use our application while the first thread is processing something.

Here we go, let’s start by creating a new application.
(Remember you can just copy/paste my code into yours, I’m just doing this way to clear up a few things).

So far, my applications looks like that:

![zsh](/img/posts/threads/threads_1.png)

Before everything make sure you included the right dependencies.

{% highlight c# %}
using System.Threading;
using System.Reflection;
{% endhighlight %}

Now, our globals.

{% highlight c# %}
Thread t;
int value = 0;
{% endhighlight %}

We should concentrate in button “Start Thread”, it will be where we start the global thread.

{% highlight c# %}
private void button1_Click(object sender, EventArgs e)
{
    t = new Thread(new ThreadStart(Run));
    t.Start();
}
{% endhighlight %}

Well, now the critical part. You know that basically a delegate is a point-to-function. I hardly recommend you to read a lot about it, it’s one of my favorite features.

First, the method we called before named Run().

{% highlight c# %}
void Run()
{
    while (true)
    {
        Thread.Sleep(1000);
        value++;
        SetControlPropertyValue(label1, "Text",
                                value.ToString());
    }
}
{% endhighlight %}

So, there’s a delegate that encapsulate the method below. There we use reflection to get the Property we want to modify, and there we go! It’s pretty simple.

{% highlight c# %}
delegate void SetControlValueCallback(Control objControl,
                    string propertyName, object newValue);

private void SetControlPropertyValue(Control objControl,
                    string propertyName, object newValue)
{
    if (objControl.InvokeRequired)
    {
        // Invoke
        SetControlValueCallback d =
                new SetControlValueCallback
                        (SetControlPropertyValue);

        objControl.Invoke(d,
                new object[] { objControl,
                        propertyName, newValue });
    }
    else
    {
        Type t = objControl.GetType();
        // Vector with all properties from object used
        PropertyInfo[] vecProperties = t.GetProperties();
        foreach (PropertyInfo p in vecProperties)
        {
            // Find the correct property to modify
            if (p.Name.ToUpper() == propertyName.ToUpper())
            {
                p.SetValue(objControl, newValue, null);
            }
        }
    }
}
{% endhighlight %}

Next button, I just added the Abort() method.

{% highlight c# %}
private void button2_Click(object sender, EventArgs e)
{
    t.Abort();
}
{% endhighlight %}

Let’s run our application now.

![zsh](/img/posts/threads/threads_2.png)

Click on the “Start” button to see how cool is that. While the number is being increased you’re able to write on the TextBox.

Clieck here if you’d like to download the entire app.

That’s what I wanted to post here today, hope it’s as clear as it is in my mind.

Please, use the comment area bellow for more informations.

Thanks for all the attention.

[threads]: http://msdn.microsoft.com/en-us/library/ms173178(v=vs.80).aspx
[reflection]: http://msdn.microsoft.com/en-us/library/ms173183(v=vs.80).aspx
[delegate]: http://msdn.microsoft.com/en-us/library/ms173171(v=vs.80).aspx

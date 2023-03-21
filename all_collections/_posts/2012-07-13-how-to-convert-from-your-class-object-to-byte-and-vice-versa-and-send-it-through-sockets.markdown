---
title:  "Your class to byte[] and vice using C#"
date:   2012-07-13 20:00:00
layout: post
categories: [c#]
---

This week I was working on a project and ended up with a simple problem. I had to send a couple of messages using UDP protocol, between a .NET and an Android application and it happened that I found out a great way to do that by playing with some XML...
<!--more-->

My problem was about sending a couple of messages through the internet using UDP protocol and as you know, when you work with Sockets you must setup a byte array and send it while the other part will get it as a byte array as well.

{% highlight c# %}
class TestXML
{
    string name;
    public string Name
    {
        get { return name; }
        set { name = value; }
    }

    string address;
    public string Address
    {
        get {return address; }
        set { address = value }
    }

    int age;
    public int Age
    {
        get {return age; }
        set {age = value}
    }
}
{% endhighlight %}

And you would like to convert an object in a byte array. It is so simple, I coded two methods one for convert in a byte array and another one to convert from a byte array in an object back.

{% highlight c# %}
private byte[] ClassToByteArray(Object objClass)
{
    try
    {
        String strXML = null;
        MemoryStream ms = new MemoryStream();
        XmlSerializer xmlS = new XmlSerializer(typeof(TestXML));
        XmlTextWriter xmlTW = new XmlTextWriter(ms, Encoding.UTF8);

        xmlS.Serialize(xmlTW, objClass);
        ms = (MemoryStream)xmlTW.BaseStream;

        return ms.ToArray();
    }
    catch(Exception)
    {
        throw;
    }
}

private Object ByteArrayToClass(byte[] buffer)
{
    try
    {
        XmlSerializer xmlS = new XmlSerializer(typeof(TestXML));
        MemoryStream ms = new MemoryStream(buffer);
        XmlTextWriter xmlTW = new XmlTextWriter(ms, Encoding.UTF8);

        return xmlS.Deserialize(ms);
    }
    catch(Exception)
    {
        throw;
    }
}
{% endhighlight %}

That’s it! I told it was so simple, I didn’t realize it was so simple before reading a little bit more about [XmlSerializer][xml]. I totally recommend it guys!

Before finishing up this tutorial, let’s run a single test to prove that it worked swell.

{% highlight c# %}
// TEST CONVERT FROM TestXML TO byte[]
TestXML objTest = new TestXML();
objTest.Name = "Felipe";
objTest.Address = "123 Street";
objTest.Age = 23;

byte[] finalValue = new byte[1464];
finalValue = ClassToByteArray(TestXML);

// VICE VERSA
TestXML objNew = new TestXML();
objNew = (TestXML)ByteArrayToClass(finalValue);
{% endhighlight %}

[xml]: https://msdn.microsoft.com/en-us/library/system.xml.serialization.xmlserializer.aspx

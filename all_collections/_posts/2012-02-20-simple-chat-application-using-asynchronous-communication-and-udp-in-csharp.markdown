---
title:  "Simple async chat application with UDP in C#"
date:   2012-02-20 20:00:00
layout: post
categories: [c#]
---

 Here I am again to show you a little bit of asynchronous communication in C#. A few months ago I ended up developing an application and learning more about asynchronous stuff. It’s pretty easy, MSDN documentation is pretty clear so I’ll try to be focus here...
 <!--more-->

 If you’d like to read before getting started, [check this out][doc].

 All right, so what exactly am I going to do here? I’m thinking of develop an application that sends a message using UDP protocol to another machine (both bound to an IP and some random port), the other machine will be listening to its port (this is the asynchronous part), get the message and show it to us. Pretty simple, huh?

 So, let’s get started with our code. I just created an application, dragged and dropped some components, it looks like this now:

 ![zsh](/img/posts/chat/chat_1.png)

 Our application will run as the sender and the receiver at the same time, to make it more beautiful, you can open 2 instances of the same app, bind each one to different ports and send the message to each other, it will probably run in different machines too, since they are in the same network. Alegre

So, before our application starts to send message to the other app, we must configure all the network stuff.

{% highlight c# %}
// == using ==
using System.Net.Sockets;
using System.Net;

// == variables ==
// used to communicate
Socket sckCommunication;
EndPoint epLocal, epRemote;

// buffer to receive info
byte[] buffer;
{% endhighlight %}

Since I’m running these apps in the same machine, both will have the same IP, here I have a simple way to get your own ip:

{% highlight c# %}
// return the own ip
private string GetLocalIP()
{
    IPHostEntry host;
    host = Dns.GetHostEntry(Dns.GetHostName());
    foreach (IPAddress ip in host.AddressList)
    {
        if (ip.AddressFamily == AddressFamily.InterNetwork)
        {
            return ip.ToString();
        }
    }
    return "127.0.0.1";
}
{% endhighlight %}

Then this is my Form_Load method:

{% highlight c# %}
private void Form1_Load(object sender, EventArgs e)
{
    // set up socket
    sckCommunication = new Socket(AddressFamily.InterNetwork,
                        SocketType.Dgram, ProtocolType.Udp);
    sckCommunication.SetSocketOption(SocketOptionLevel.Socket,
                        SocketOptionName.ReuseAddress, true);

    // get own ip
    txtLocalIp.Text = GetLocalIP();
    txtFriendsIp.Text = GetLocalIP();
}
{% endhighlight %}

Going to our button START onto its click method, here is what I have:

{% highlight c# %}
private void btnStart_Click(object sender, EventArgs e)
{
    // bind socket                        
    epLocal = new IPEndPoint(IPAddress.Parse(txtLocalIp.Text),
                            Convert.ToInt32(txtLocalPort.Text));
    sckCommunication.Bind(epLocal);

    // connect to remote ip and port
    epRemote = new IPEndPoint(IPAddress.Parse(txtFriendsIp.Text),
                            Convert.ToInt32(txtFriendsPort.Text));
    sckCommunication.Connect(epRemote);

    // starts to listen to an specific port
    buffer = new byte[1464];
    sckCommunication.BeginReceiveFrom(buffer, 0, buffer.Length,
                             SocketFlags.None, ref epRemote,
                    new AsyncCallback(OperatorCallBack), buffer);

    // release button to send message
    btnSend.Enabled = true;
}
{% endhighlight %}

Simply I have created two EndPoints, one for local and another for remote, so I’m binding my socket to local information and connecting it to remote information, in this case, I am able to send and receive information from this Socket. And last, I called a method called (lol) BeginReceiveFrom which represents an asynchronous method. In a few words, this method gets a thread and keeps it running until something happen, once it does, it calls a delegate I called OperatorCallBack, which is an [AsyncCallback][async] delegate.

{% highlight c# %}
private void OperatorCallBack(IAsyncResult ar)
{
    try
    {
        int size = sckCommunication.EndReceiveFrom(ar, ref epRemote);

        // check if theres actually information
        if (size > 0)
        {
            // used to help us on getting the data
            byte[] aux = new byte[1464];

            // gets the data
            aux = (byte[])ar.AsyncState;

            // converts from data[] to string
            System.Text.ASCIIEncoding enc =
                                    new System.Text.ASCIIEncoding();
            string msg = enc.GetString(aux);

            // adds to listbox
            listBox1.Items.Add("Friend: " + msg);                   
        }

        // starts to listen again
        buffer = new byte[1464];
        sckCommunication.BeginReceiveFrom(buffer, 0,
                            buffer.Length, SocketFlags.None,
            ref epRemote, new AsyncCallback(OperatorCallBack), buffer);
    }
    catch (Exception exp)
    {
        MessageBox.Show(exp.ToString());
    }
}
{% endhighlight %}

Almost done now, before we forget, we are not sendint any information yet, so go to you SEND button and it should look like this:

{% highlight c# %}
private void btnSend_Click(object sender, EventArgs e)
{                      
    // converts from string to byte[]
    System.Text.ASCIIEncoding enc =
            new System.Text.ASCIIEncoding();
    byte[] msg = new byte[1464];
    msg = enc.GetBytes(txtMessage.Text);

    // sending the message
    sckCommunication.Send(msg);

    // add to listbox
    listBox1.Items.Add("You: " + txtMessage.Text);

    // clear txtMessage
    txtMessage.Clear();
}
{% endhighlight %}

I believe the first time you run your application if you have your firewall enabled, Windows will ask if you allow your app to use its resources like this:

![zsh](/img/posts/chat/chat_2.png)

Just allow its access. Running our application!

![zsh](/img/posts/chat/chat_3.png)
![zsh](/img/posts/chat/chat_4.png)
![zsh](/img/posts/chat/chat_5.png)

Pretty easy, right? Hope you like this post.

[doc]: http://msdn.microsoft.com/en-us/library/ms734701.aspx
[async]: https://msdn.microsoft.com/en-us/library/system.asynccallback.aspx

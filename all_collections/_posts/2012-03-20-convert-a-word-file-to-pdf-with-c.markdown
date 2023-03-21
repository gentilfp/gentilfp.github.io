---
title:  "Convert a MS Word file to PDF using C#"
date:   2012-03-20 20:00:00
layout: post
categories: [c#]
---

A couple of weeks has passed since my last post, but here I am again to give you a very easy and nice way to convert a Word file (by Word, read .docx, .doc, .rtf, etc) to a PDF. I have searched a lot on the internet about conversion to PDF and it’s not something easy, although it is simple to understand this code I am going to give...
<!--more-->

First of all, to get this tutorial working correctly, you MUST have Office installed in your computer. Otherwise it won’t work. You have plenty of other tutorials even using [iTextSharp][itextsharp] to create your PDF or Java using [JodConverter][jod] and OpenOffice like [this][this].

So let’s get it started.

We create a new project and add Microsoft.Office.Interop.Word as refference like showing in the pictures bellow.

![zsh](/img/posts/word_pdf/img_1.png)

{% highlight c# %}
using System.IO;
using Microsoft.Office.Interop.Word;
{% endhighlight %}

I created a form just to make things easier, but what really matters in this tutorial is the method called ConvertWord2PDF.

![zsh](/img/posts/word_pdf/img_2.png)

Okay, form already setup. We should go to our main method. Basically, to convert a Word file to PDF we must:

* Open
* Export
* Close

So, that’s what we have by following this order.

{% highlight c# %}
private void ConvertWord2PDF(string inputFile, string outputPath)
{
    try
    {
        if (outputPath.Equals("") || !File.Exists(inputFile))
        {
            throw new Exception("Either file does not exist or invalid output path");
        }

        // app to open the document belower
        Microsoft.Office.Interop.Word.Application wordApp = new Microsoft.Office.Interop.Word.Application();
        Document wordDocument = new Document();

        // input variables
        object objInputFile = inputFile;
        object missParam = Type.Missing;                

        wordDocument = wordApp.Documents.Open(ref objInputFile, ref missParam, ref missParam, ref missParam,
            ref missParam, ref missParam, ref missParam, ref missParam, ref missParam, ref missParam,
            ref missParam, ref missParam, ref missParam, ref missParam, ref missParam, ref missParam);

        if (wordDocument != null)
        {
            // make the convertion
            wordDocument.ExportAsFixedFormat(outputPath, WdExportFormat.wdExportFormatPDF, false,
                WdExportOptimizeFor.wdExportOptimizeForOnScreen, WdExportRange.wdExportAllDocument,
                0, 0, WdExportItem.wdExportDocumentContent, true, true,
                WdExportCreateBookmarks.wdExportCreateWordBookmarks, true, true, false, ref missParam);                                                    
        }

        // close document and quit application
        wordDocument.Close();
        wordApp.Quit();

        MessageBox.Show("File successfully converted");
        ClearTextBoxes();
    }
    catch (Exception e)
    {                
        throw e;
    }
}
{% endhighlight %}

Well, that’s pretty much what you need. I just want to focus on both methods, Open and ExportAsFixedFormat. There’s a bunch of parameters we are not using, if you want to understand one by one, all you need is to read the documentation.

Here we have our Open method.

![zsh](/img/posts/word_pdf/img_3.png)

And here the ExportAsFixedFormat method.

![zsh](/img/posts/word_pdf/img_4.png)

Finally, testing our application and…

![zsh](/img/posts/word_pdf/img_5.png)

Easy, huh? I know its restriction about the need of Microsoft Office installed but it’s the simplest way I have ever seen.Hope you guys get use of this tutorial, you can download the whole application [here][app].

Thanks for all the attention.


[itextsharp]: https://sourceforge.net/projects/itextsharp/
[jod]: https://code.google.com/archive/p/jodconverter/
[this]: http://www.artofsolving.com/node/16
[app]: http://dl.dropbox.com/u/5986982/Word2Pdf.rar

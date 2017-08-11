---
layout: single
title: "Resharper plug-in: Minify XML"
date: 2012-02-10 20:48:00 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/02/10/resharper-plug-in-minify-xml-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2012/02/10/resharper-plug-in-minify-xml-aspx/.html
 - /2012/02/10/resharper-plug-in-minify-xml-aspx/.html
---
<h1>The Problem</h1>

<p>I've been working on a little tool that uses WCF serialization lately and had to deal with these XML files representing deserialized datacontracts. One of the requirements of the tool is that some users should be able to feed it with such XML files. Think about it as feeding the tool with an input XML file, processing it to create some output, and compare the output with some other XML file that represents the expected output. You figured it's some kind of testing tool right? :)</p>

<p>Anyway, the expected output and the actual output are being serialized when written to disk, and deserialized when read. The default settings for the WCF DataContractSerializer make sure that the entire deserialized XML string gets saved into one single line. I know you can tweak these settings and <a href="http://stackoverflow.com/questions/739114/formatting-of-xml-created-by-datacontractserializer" target="_blank">change the formatting of the XML created by the DataContractSerializer</a>, but if that's not an option, you'll have to make sure those files respect the expected format.</p>

<p>You have to know that some of the users of the tool have Visual Studio + ReSharper, so these guys will be tempted to open the XML files and change a little value here and there before saving the file and feeding it again to the tool. That's where formatting comes in: if a user wants to get a nice overview of what's inside the file and easily modify a value, he'll just format the document make the job easier. However, this document must be <em>minified</em> again on one single line for the tool to be able to deserialize the file. That's when it struck me that minifying CSS and JS is supported by various tools, but none is available for XML.</p>

<h1>My Solution</h1>

<p><img width="529" height="148" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; margin-right: 0px; margin-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1410.png" /></p>

<p>Now that's a great tip, thank you <a href="http://www.paulstack.co.uk/blog/" target="_blank">Paul</a>! When he told me it would be easy to do so using the <a href="http://download.jetbrains.com/resharper/ReSharperSDK-6.1.0.51.msi" target="_blank">ReSharper 6.1 SDK</a>, I decided to give it a shot and time-box it to an hour. One hour only!</p>

<p>Hence, I installed the SDK, which on first sight looked pretty complete, including samples, project templates and a whole lot of item templates. Awesome! Creating a new ReSharper Plug-In project is really peanuts: it even is preconfigured to debug the project against the <em>Visual Studio Experimental Hive</em>, passing it the necessary parameters to plug-in your plug-in and enabling some hidden internal Resharper diagnostic windows.</p>

<p><a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1420.png" target="_blank"><img width="650" height="213" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1420.png" /></a></p>

<p><a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1425.png"><img width="650" height="525" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; margin-right: 0px; margin-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1425.png" /></a></p>

<p>In addition to that, <a href="http://hadihariri.com/" target="_blank">Hadi Hariri</a> has a great <a href="http://hadihariri.com/2010/01/12/writing-plug-ins-for-resharper-part-1-of-undefined/" target="_blank">series on writing plug-ins for Resharper</a>, amongst which the first one helped my quite a long way to implementing my own plug-in. In less than a few minutes, I had my solution set up and could focus on the work at hand: minify some XML. That's really a great experience to get started with something you've never done before!</p>

<p>Simply put, the plug-in needs to know when it is available, and needs to know what it has to do. The availability part is easy: I want to be able to minify the entire file, so making it available in the root XML node makes sense. The execution itself, is pretty straightforward as well: take the entire XML element (including its child nodes if any) and replace it with a minified version, leaving the XML structure intact. That's just a matter of pulling a RegEx monkey out of your sleeves and make sure those whitespaces, tabs and carriage returns get removed.</p>

<h1>Minify XML</h1>

<p>Minifying is not necessarily equal to <em>obfuscating</em> or <em>compressing</em>. When it comes to XML, the element names and attribute names usually are meaningful as well. Changing this would definitely break deserialization of such files. Hence, I only had to take care of the whitespace formatting part.</p>

<p>Minifying the XML is done using a tiny XmlMinifier class shown below:</p>

<div class="wlWriterEditableSmartContent" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:4f518378-2c14-4403-9c9d-ff200adf0713" style="margin: 0px; display: inline; float: none; padding: 0px;">
  <pre class="brush: c#;">    public class XmlMinifier : IMinifier
    {
        public string Minify(string input)
        {
            // Remove carriage returns, tabs and whitespaces
            string output = Regex.Replace(input, @"\n|\t", " ");
            output = Regex.Replace(output, @"&gt;\s+&lt;", "&gt;&lt;").Trim();
            output = Regex.Replace(output, @"\s{2,}", " ");

            // Remove XML comments
            output = Regex.Replace(output, "&lt;!--.*?--&gt;", String.Empty, RegexOptions.Singleline);

            return output;
        }
    }</pre>
</div>

<p>For the full implementation details, I'll invite you to take a look at the <a href="https://github.com/xavierdecoster/Resharper-XML-Minifier" target="_blank">GitHub repository</a>. It's only something like 100 lines of code amongst which the most meaningful are probably stated above. You might wonder what I did the rest of that hour :)</p>

<h1>How to use it</h1>

<p>Once the plug-in is installed, and you open any XML file into Visual Studio, you'll see a new option appearing when you put your cursor in the root XML element.</p>

<p><img width="266" height="152" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1518.png" /></p>

<p>Alt-Enter (don't you dare to use the mouse!) and select <strong>Minify file</strong>.</p>

<p><img width="127" height="99" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1519.png" /></p>

<p>There you go!</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1521.png" alt="" /></p>

<h1>Give me the goodies</h1>

<p>Installing the plug-in is easy as well: simply <a href="https://github.com/downloads/xavierdecoster/Resharper-XML-Minifier/Resharper.Plugins.Minify.dll">fetch the dll</a> and put it in the following location:C:\Program Files (x86)\JetBrains\ReSharper\v6.1\Bin\PluginsDon't forget to <strong>unblock</strong> the file after downloading it because it might be very unsafe! :)You can verify if the plug-in got installed correctly by navigating to <strong>ReSharper > Tools > Options</strong> and select Plug-ins.</p>

<p><img width="500" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-02-10/2012-02-10_1514.png" /></p>

<h1>Potential for Optimization</h1>

<p>I think it would be cool if I could just minify a selected element and leave the rest of the file formatting untouched. However, in order for this to work without any strange behavior, I need to be able to save the file after each execution of the plug-in (or at least update the cache, because ReSharper seems to work on the cached source file as long as the file isn't saved). If anyone has an idea on how to do this (â€˜cause I couldn't find a working sample), please reach out or submit a patch :-)</p>

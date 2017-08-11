---
layout: single
title: "Hidden gem: the NuGet package manifest XSD"
date: 2012-03-08 21:16:00 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/03/08/hidden-gem-the-nuget-package-manifest-xsd/"]
author: Xavier Decoster
redirect_from:
 - /2012/03/08/hidden-gem-the-nuget-package-manifest-xsd/.html
 - /2012/03/08/hidden-gem-the-nuget-package-manifest-xsd/.html
---
<p>If you are automating the creation of <a href="http://www.nuget.org" target="_blank">NuGet</a> packages (through CI for instance), you most likely touched a <strong>.nuspec</strong> file somehow. The .nuspec file is an XML file representing the NuGet package manifest, describing your package contents and metadata.</p>

<h2>Creating the NuSpec</h2>

<p>The most easy way of creating a NuGet package manually is through using the <a href="http://npe.codeplex.com" target="_blank">NuGet Package Explorer</a>. It's simply a great UI on top of your packages and a good way of learning about some of the conventions that you need to follow in order to produce a valid NuGet package. It just works.</p>

<p>If you need to make big changes or want to add dependencies to other packages, NuGet Package Explorer is the preferred modification tool for these manifests. However, if you only need to modify the file very slightly, it's often faster to simply modify the XML. I'm sure many of us don't do this because there's quite a few options available and not everyone is familiar with the structure and conventions of this file. If only there was an <strong>XSD</strong> that could provide you with some Visual Studio Intellisense sugarâ€¦ Hey, you know what, that XSD does exist! Actually, there's even more than one.</p>

<h2>But why?</h2>

<p>In the scenario of <strong>automation</strong>, there's no man in the middle that is going to click around in the UI for you, so you take a different approach. That's where the NuGet command line (nuget.exe) comes in. There are various ways of creating a package using the command line, and you don't always have to provide it with a nuspec file upfront (it can create one for you by deriving project or assembly information).</p>

<p>My preference goes to creating the package manifests upfront, manually, in XML. You have full control over its contents, and can create more advanced packages that simply cannot be created for you based on your assembly information, which provides a very limited amount of information. Put these .nuspec files in your VCS, and you have a versioned history on your package manifest modifications as well. Typically, I put them into the <em>.nuget</em> folder in my solution directory, which is created for you when you use the <a href="http://blog.davidebbo.com/2011/03/using-nuget-without-committing-packages.html" target="_blank">Enable-Package-Restore feature</a> (also known as the no-commit rule).</p>

<h2>I'm done reading, give me the XSD!</h2>

<p>If you dive into the <a href="http://nuget.codeplex.com/" target="_blank">NuGet source code</a> (and really, Codeplex, where's that code search box?), you'll find the XSD in the <em>src\Core\Authoring\nuget.xsd</em> path. Now, before you fetch it, remember I said there's more than one? Actually, the one you'll find in the sources is a template that changes over time (as new features are added for instance). The XML namespace actually contains a placeholder which is replaced during release. So <em>*don't *</em>use the file from Codeplex. You don't know whether it has changed since the last release.</p>

<p>To make things easier for you, I've done a look-up on the various XML namespace that are in use, and which version of the XSD matches. <a href="https://nuget.org/packages/NuGet.Manifest.Schema/2.0.0" target="_blank">The end result is a NuGet package</a> (what else?), containing the two XSD's and a sample .nuspec file to get you started.</p>

<p>Simply run the following command from the NuGet Package Manager Console and spend some quality time designing your package:</p>

<div class="wlWriterEditableSmartContent" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:2375e2a4-da21-48ba-9753-39baee98e61d" style="margin: 0px; display: inline; float: none; padding: 0px;">
  <pre class="brush: bash;gutter:false;">Install-Package NuGet.Manifest.Schema</pre>
</div>

<p>The real benefit comes with Visual Studio's ability to automatically provide Intellisense for your file when it is declared in an XML-namespace it knows. For instance, when the XSD is available in your solution. Enjoy!</p>

<p><img width="580" height="514" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-08/2012-03-08_2027.png" /></p>

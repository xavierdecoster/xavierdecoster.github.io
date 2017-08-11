---
layout: single
title: "Using MyGet as a OneGet package source"
date: 2014-04-17 17:03:10 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2014/04/17/using-myget-as-a-oneget-package-source/"]
author: Xavier Decoster
redirect_from:
 - /2014/04/17/using-myget-as-a-oneget-package-source/.html
 - /2014/04/17/using-myget-as-a-oneget-package-source/.html
---
<p>At the Build conference, Microsoft announced the <a href="http://www.microsoft.com/en-eg/download/details.aspx?id=42316" target="_blank" title="Windows Management Framework 5.0 Preview">Windows Management Framework 5.0 Preview</a> which includes Windows PowerShell 5.0, updates to the PowerShell ISE, Network Switch Cmdlets and ... <b>OneGet</b>!</p>

<h1>What is OneGet?</h1>


<blockquote>OneGet a unified package management interface component with a set of managed and native APIs, a set of PowerShell cmdlets, and a WMI provider. The component accepts both Microsoft-provided and 3rd party-provided plugins which extend the functionality for a given package type.
</blockquote>


<p>The OneGet team also has a weekly community meeting of which you can see the first introductionary recording below.</p>

<iframe width="640" height="360" src="//www.youtube-nocookie.com/embed/r0yfCSAGCLM" frameborder="0" allowfullscreen></iframe>

<p>As part of this Preview, OneGet is shipping with a prototype plugin compatible with Chocolatey, the so called <i>ChocolateyProvider</i>. This is a prototype implementation of a Chocolatey-compatible package manager that can install existing Chocolatey packages. This is a clear confirmation for the hard work done by the Chocolatey folks, and both systems will continue to evolve together, as <a href="https://twitter.com/ferventcoder" target="_blank">Rob Reynolds</a> explains in <a href="https://groups.google.com/forum/#!topic/chocolatey/a8WdEoF-M58" target="_blank">this post</a>. If you want to follow-up on OneGet, then check out <a href="https://github.com/OneGet/oneget" target="_blank">its GitHub repository</a> and <a href="https://twitter.com/PSOneGet" target="_blank">follow PSOneGet</a> on Twitter.</p>

<h1>Something about a forest and trees...</h1>

<p>NuGet, MyGet, Chocolatey, OneGet... what?! People ask questions and occasionally can't see the forest for the trees. Here's a quick recap:</p>

<ul>
<li><b>NuGet</b>: a <i>solution-level</i> package management tool, used to manage software dependencies within the scope of a solution. It is accompanied by the <a href="http://nuget.org" target="_blank">NuGet Gallery</a>, the home of many if not all .NET open source components.</li>
<li><b>Chocolatey</b>: a <i>system-level</i> package management tool, used to manage software installations on a Windows system. It (currently) leverages PowerShell and NuGet, supports the Web Platform Installer (WebPI), MSI, RubyGems and many more, and is accompanied by the <a href="http://chocolatey.org" target="_blank">Chocolatey Gallery</a> where you can find many popular software packages. Rob describes Chocolatey as somewhat like "apt-get", but with Windows in mind.</li>
<li><b>MyGet</b>: a <i>hosted NuGet package server</i> where you can create and secure your own feeds. In essence, <a href="https://www.myget.org" target="_blank">MyGet</a> is able to host vanilla NuGet feeds, <a href="http://docs.myget.org/docs/reference/package-sources" target="_blank">as well as Chocolatey feeds</a>.</li>
<li><b>OneGet</b>: a <i>a unified interface to package management systems</i> (see above)</li>
</ul>

<p>So what does this mean? How do these package managers play along?</p>

<p>OneGet supports multiple package sources, and as stated earlier, OneGet comes with a <i>ChocolateyProvider</i>. As MyGet also supports Chocolatey feeds, this effectively means that you can register a MyGet feed as a Chocolatey package source in OneGet! The below diagram is an attempt to illustrate how they relate:</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2014-04-18/2014-04-17_1958.png" alt="How do OneGet, Chocolatey, NuGet and MyGet play along?" style="max-width:650px;"/></p>

<p>OneGet supports several commands at this stage:</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2014-04-18/2014-04-04_1058.png" alt="OneGet Preview cmdlets" style="max-width:650px"/></p>

<h1>How can I use a private OneGet package source?</h1>

<p>So how can I register a private OneGet package source? Well, let's first see how you can register any package source using the Add-PackageSource cmdlet. Here's what the built-in help currently says:</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2014-04-18/2014-04-04_1101.png" alt="OneGet Add-PackageSource Help" style="max-width:650px;"/></p>

<p>Note that this is a Preview: help is incomplete and cmdlets might change name, but this should already give you a good idea of what you can do with this cmdlet!</p>

<p>Now, let's register a MyGet feed on which you can host Chocolatey packages:</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2014-04-18/2014-04-04_1128.png" alt="Register a MyGet feed as a OneGet package source" style="max-width:650px;"/></p>

<p>Did you notice how OneGet asked you to install the NuGet package manager?</p>

<p>That went easy right? That's because that feed was public :) OneGet does not support basic-authentication at this point, nor does it leverage any nuget.config settings you might configure (tried it). However, MyGet just <a href="http://docs.myget.org/docs/reference/feed-endpoints" target="_blank">added the possibility to use a "private-by-obscurity" endpoint on private feeds</a>, which should allow you to use private feeds as well. Note: we don't actively promote this, as it requires you to share one of your feed's access tokens. This is a work-around for clients that don't support the basic-auth flow, and we'd prefer to have proper basic-authentication support in OneGet, so fingers crossed!</p>

<p>You can verify the correct registration of your OneGet package source using the following commands:</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2014-04-18/2014-04-04_1129.png" alt="Get OneGet package sources" style="max-width:650px;"/>
<img src="https://xavierdecosterblog.blob.core.windows.net/blog/2014-04-18/2014-04-04_1134.png" alt="List available packages on a OneGet package source" style="max-width:650px;"/></p>

<p>Installing a software package from this MyGet feed is straightforward as well:</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2014-04-18/2014-04-04_1141_001.png" alt="Install a software package from a specific OneGet feed" style="max-width:650px;"/></p>

<p>This flow allows you to control what packages get distributed through OneGet, avoids the need to publish your internal software to the general public, and still allows you to leverage the great new scenarios that OneGet offers!</p>

<p>As usual, happy packaging! :)</p>

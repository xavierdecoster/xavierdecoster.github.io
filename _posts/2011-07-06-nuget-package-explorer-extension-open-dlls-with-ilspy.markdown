---
layout: post
title: "NuGet Package Explorer extension: open dlls with ILSpy"
date: 2011-07-06 19:21:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/07/06/nuget-package-explorer-extension-open-dlls-with-ilspy/"]
author: Xavier Decoster
redirect_from:
 - /2011/07/06/nuget-package-explorer-extension-open-dlls-with-ilspy/.html
 - /2011/07/06/nuget-package-explorer-extension-open-dlls-with-ilspy/.html
---
<p>One of the things I find useful is being able to decompile an assembly and explore its inner workings. I bet you all are familiar with such tools as <a href="http://www.jetbrains.com/decompiler/" target="_blank">DotPeek</a>, <a href="http://wiki.sharpdevelop.net/ILSpy.ashx" target="_blank">ILSpy</a>, and the evil one which shall remain unnamed..</p>

<p>If you want to explore assemblies which are packaged into a <a href="http://www.nuget.org/" target="_blank">NuGet</a> package, you have to go through a manual process:</p>

<p>You unzip the entire package, you find the dll you need in explorer, you open it in your favorite decompiler. Of course, you can also just open the package in <a href="http://npe.codeplex.com/" target="_blank">Package Explorer</a>, and save the assemblies you want into some location, then go to explorer, find your assemblies back and open them respectively.</p>

<p>Now, this is just cumbersome. People can call us lazy, developers now we have a name for it, even a&nbsp;<em>best practice</em>: it's <a href="http://en.wikipedia.org/wiki/Don't_repeat_yourself" target="_blank">DRY</a>!</p>

<p>So those are my motivations to come up with a little extension to Package Explorer that can make your life easier. I've targetted ILSpy as my decompilation tool, simply because it also supports commandline arguments.</p>

<p><strong>Project description</strong></p>

<p>This extension to NuGet Package Explorer allows you to quickly open dll's from the package into ILSpy, without the need to extract the package, go into explorer, and open it up manually.</p>

<p><strong>Getting started</strong></p>

<p>To install the plugin, just open up <a class="externalLink" href="http://npe.codeplex.com">NuGet Package Explorer</a> and select Tools &gt; Plugin Manager...</p>

<p><img style="width: 600px;" src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-06/npe_pluginmgr.png" /></p>

<p>Add the <a class="externalLink" href="http://npeilspy.codeplex.com/releases">downloaded library</a> in the list of loaded plugins and you're good to go.</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-06/npe_loadedplugins.png" /></p>

<p>Just double click an assembly inside an NuGet package and it will prompt you to open it in ILSpy.</p>

<p><img style="width: 600px;" src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-06/npe_openassembly.png" /></p>

<p><img style="width: 600px;" src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-06/npe_ilspy.png" /></p>

<p>Of course you need to have <a class="externalLink" href="http://wiki.sharpdevelop.net/ILSpy.ashx">ILSpy</a> on your computer (as well as <a class="externalLink" href="http://npe.codeplex.com">NuGet Package Explorer</a> 1.5 or above).</p>

<p><strong>Bits and pieces</strong></p>

<p>This extension is open-source and available for you on&nbsp;<a href="http://npeilspy.codeplex.com" target="_blank">http://npeilspy.codeplex.com</a>.</p>

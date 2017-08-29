---
layout: single
title: "NuGet.Analyze now available as command line extension"
date: 2011-10-07 20:00:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["ALM","NuGet","TFS","Open source","Package Management"]
alias: ["/2011/10/07/nuget-analyze-now-available-as-command-line-extension-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2011/10/07/nuget-analyze-now-available-as-command-line-extension-aspx/.html
 - /nuget-analyze-now-available-as-command-line-extension
---
<p><strong>2011-10-20 Edit: the NuGet.Analyze.Installer package is now obsolete, as explained in <a href="/post/2011/10/20/Install-NuGet-command-line-extensions-using-the-Package-Manager-Console.html" target="_blank">this post</a>. Use the more generic installation procedure provided by the NuGet.InstallCommandLineExtension package instead.</strong></p>

<h1>Extending the command line</h1>

<p>Earlier this week, I <a href="/post/2011/10/06/Generate-package-dependency-matrix-directly-from-TFS-source-control.html" target="_blank">posted an article</a> about a proof-of-concept I'm working on. I want to analyze a source control repository for <a href="http://www.nuget.org" target="_blank">NuGet</a> package dependencies and generate some kind of dependency matrix out of it. My proof-of-concept is targeting Team Foundation Server, as it is the most usable scenario for me to support in the short term, but I'd like to be able to do this against any type of source control system in the future.</p>

<p>As it is just a proof-of-concept, I quickly forked the NuGet repository on Codeplex and rambled a piece of code together that did what I wanted to test. This was very quick and even more dirty. Especially the added dependencies to the TFS SDK must have been heart-breaking for the NuGet command line. Sorry about that :-)</p>

<p>However, I noted the usage of MEF and a constant for a command line extensions folder in the sources. About at the same time, my colleague <a href="http://blog.maartenballiauw.be" target="_blank">Maarten Balliauw</a> reminded me about the possibility to extend the NuGet command line. This feature was added in NuGet version 1.4 and has not yet received all the attention it deserves. Another hookpoint to exploit! (read: use properly :-)) If you want to learn how, please take a look at Rob Reynolds (<a href="http://twitter.com/#!/ferventcoder" target="_blank">@ferventcoder</a>) <a href="http://geekswithblogs.net/robz/archive/2011/07/15/extend-nuget-command-line.aspx" target="_blank">great post on this topic</a> for full details.</p>

<p>Knowing this, I thought it was a good idea to refactor this command into a command line extension and package it up for consumption. People who want to try it out can go grab it already, but please be aware that it is a <em>proof-of-concept</em> that deserves more testing and tweaking. However, I find feedback from actual users to be most valuable.</p>

<h1>Installing the command line extension</h1>

<p>There was one thing I didn't like about my package though: the distribution process. My package is adding an extension to the command line, and is not adding a dependency to any target project or solution. I'm crossing the boundaries here of application-level package management, something NuGet was originally designed for. If you've read my blog before, you're probably already aware of the fact that I'm looking at NuGet from a different perspective: NuGet as a protocol.</p>

<p>I learned about an <a href="http://nuget.org/List/Packages/AddConsoleExtension" target="_blank">AddExtension</a> command line extension for NuGet, which should ease this pain. One problem though: installing this extension doesn't feel natural at all! This is the description of the package:</p>

<p><em>To use this package, install the package using NuGet.exe to %LocalAppData%\NuGet\Commands NuGet.exe Install /ExcludeVersion /OutputDir %LocalAppData%\NuGet\Commands AddConsoleExtension</em></p>

<p>Really? I know, strictly speaking, this is a perfectly valid way to install a NuGet package. It is also a way of saying: "You're extending the command line anyway, so use it!". Perfectly fine for me, but what happens if you try to install this package using the Package Manager Console from within Visual Studio? Or using the Package Manager dialogs? You don't get the same results! Even worse, the package is not correctly installed as the package producer intended!</p>

<p>That's why I came up with an installer for my package: a nuget package that installs another nuget package :-) Actually, my idea got inspired by <a href="http://twitter.com/#!/davidfowl" target="_blank">David Fowler</a>'s <a href="http://nuget.org/List/Packages/NuGetPowerTools" target="_blank">NuGetPowerTools</a>, which is having the exact same behavior: it installs functionality, not a dependency. If you would unzip the <a href="http://packages.nuget.org/v1/Package/Download/NuGetPowerTools/0.29" target="_blank">NuGetPowerTools nupkg</a>, you'd notice there's only a bunch of PowerShell scripts. Diving a bit deeper revealed something very noteworthy: the PowerShell scripts are installing <a href="http://nuget.org/List/Packages/NuGet.Build" target="_blank">another package</a>!</p>

<p>Hence, all the pieces required to make things smoother were falling together: I could design my command line extension package just like any other NuGet package, and have an installer package that installs it where I want it to be installed. If you still decide to install my package without using my installer package, you basically decided you wanted to reference it in your project. Either way, both of my packages will always have the same installation results, whether you use the NuGet command line, the Package Manager Console or the GUI.</p>

<p>To install the <a href="http://nuget.org/List/Packages/NuGet.Analyze" target="_blank">NuGet.Analyze</a> command line extension, run the following script:</p>

<ul>
<li>from the Package Manager Console: <em>Install-Package NuGet.Analyze.Installer</em></li>
<li>from the command line: <em>nuget install NuGet.Analyze.Installer</em></li>
</ul>

<p>Or just install the <a href="http://nuget.org/List/Packages/NuGet.Analyze.Installer" target="_blank">NuGet.Analyze.Installer</a> package using the GUI in Visual Studio.</p>

<p>The commands are much more concise, the behavior is consistent! I like.</p>

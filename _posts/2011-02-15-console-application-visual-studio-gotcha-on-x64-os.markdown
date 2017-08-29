---
layout: single
title: "Console Application - Visual Studio gotcha on x64 OS"
date: 2011-02-15 17:22:00 +0100
comments: true
published: true
categories: ["post"]
tags: ["Visual Studio"]
alias: ["/2011/02/15/console-application-visual-studio-gotcha-on-x64-os/"]
author: Xavier Decoster
redirect_from:
 - /2011/02/15/console-application-visual-studio-gotcha-on-x64-os/.html
 - /console-application-visual-studio-gotcha-on-x64-os
---
<p>I was playing around a bit with <a href="http://entlib.codeplex.com/" target="_blank">Enterprise Library 5.0</a> and it's Logging block. I figured it would be a very simple scenario: Enterprise Library is there to assist us with the most common practices isn't it?</p>

<p>I created a solution with a Database project, a Class Library project and a very simple Console Application project, while following the <a href="http://msdn.microsoft.com/en-us/library/ff664543%28v=PandP.50%29.aspx" target="_blank">Enterprise Library documentation to log to a database</a>.</p>

<p>So far so good:</p>

<ul>
<li>The database was deployed properly with all required tables, keys and stored procedurs.</li>
<li>The console application had it's proper entlib-configuration.</li>
</ul>

<p>However, when running the console app, I noticed that nothing was written to the database. No exceptions were thrown either. I checked, double checked and triple checked the configuration: all seemed fine. Spooky!</p>

<p>Hours of my time went into debugging the 5 lines counting program until I noticed the Build output: *"1 project skipped". *It was the class library that contained my LoggingProvider.</p>

<p>Damn! Everything compiled, no exceptions, no config errors... Only that Visual Studio decided to not compile my class library!</p>

<p>I was developing on a Windows 7 x64 operating system. I first created the class library project and then added a new C# Console Application project to the solution.</p>

<p>It turns out: <a href="http://connect.microsoft.com/VisualStudio/feedback/details/455103/new-c-console-application-targets-x86-by-default" target="_blank">new C# Console application projects target the x86 platform by default</a>!</p>

<p><span style="font-family: verdana,geneva;"><em>"When creating a new Visual C# Console Application in VS2010 for .NET 4.0, the default target settings for the project is to target the x86 platform instead of Any CPU (MSIL) like Visual Studio 2008 does"</em></span></p>

<p>I fixed the issue by targetting the x86 platform in my class library: all of the sudden, my console app was logging to the database using EntLib 5.0...</p>

<p>Unexpected gotcha!</p>

<p><img alt="" src="/images/2010-02-15/2011-2-targetting_x86_platform.png" width="650" height="231" /></p>

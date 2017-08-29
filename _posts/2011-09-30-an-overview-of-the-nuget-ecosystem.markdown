---
layout: single
title: "An overview of the NuGet ecosystem"
date: 2011-09-30 19:40:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["Community","NuGet","Package Management"]
alias: ["/2011/09/30/an-overview-of-the-nuget-ecosystem/"]
author: Xavier Decoster
redirect_from:
 - /2011/09/30/an-overview-of-the-nuget-ecosystem/.html
 - /an-overview-of-the-nuget-ecosystem
---
<p>Many people are starting to realize what an awesome tool NuGet really is. If you don't believe me, just take a look the <a href="http://stats.nuget.org" target="_blank">statistics</a>. Looking at the NuGet ecosystem out there, I figured it might be a good idea to summarize it in a blogpost, for those who are experiencing trouble trying to keep up. If you don't know where to start, check the overview below and head over to the <a href="http://docs.nuget.org" target="_blank">NuGet documentation</a>.</p>

<ul>
<li><p><strong>NuGet -</strong> The <a href="http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c" target="_blank">NuGet Visual Studio extension</a>, available in the Visual Studio Gallery, comes with a nice integrated experience that makes managing NuGet packages as simple as adding a reference. You can customize its settings, you can perform most standard operations from a graphical user interface, and you can do more advanced stuff using the Package Manager Console. This PowerShell-enabled console is opening the door to Visual Studio automation and is an example of how NuGet can be so much more than <em>just</em> a package manager. Scaffolding is a great example of this.</p></li>
<li><p><strong>NuGet Gallery</strong> - The <a href="http://www.nuget.org" target="_blank">nuget.org</a> website allows you to create an account and get an API key to start publishing packages to the official NuGet feed. You can also query the NuGet Gallery and find more information about packages and their creators. As a package producer, this is also the place where you will find download statistics.</p></li>
<li><p><strong>NuGet Package Explorer</strong> - This <a href="http://npe.codeplex.com/releases/view/68211" target="_blank">nice and handy tool</a> allows you to open, create, publish and validate NuGet packages from within a graphical user interface. It supports plug-ins allowing you to extend its functionality even further. If you don't feel comfortable playing around with the NuGet manifest XML file (*.nuspec), then this is the right tool for you.</p></li>
<li><p><strong>NuGet Command Line -</strong> The <a href="http://www.nuget.org/List/Packages/NuGet.CommandLine" target="_blank">NuGet command line tool</a> allows you to perform operations against NuGet repositories or Visual Studio solutions/projects. It comes in really handy in continuous (package) integration scenarios. The <a href="http://www.nuget.org/List/Packages/NuGetPowerTools" target="_blank">NuGetPowerTools</a>, which will become part of NuGet itself, makes good use of it.</p></li>
<li><p><strong>NuGet.Server package</strong> - Allows you to set up your own <a href="http://www.nuget.org/List/Packages/NuGet.Server" target="_blank">NuGet server</a> fast and easy and exposes a single feed. Another way is to <a href="https://github.com/NuGet/NuGetGallery" target="_blank">create your own NuGet Gallery</a>.</p></li>
<li><p><strong>MyGet</strong> - <a href="http://www.myget.org" target="_blank">MyGet</a> makes package management even easier, by providing you with NuGet-as-a-Service. Log in, create a feed and start uploading or consuming your packages right away! No need to set up a NuGet server yourself, and you benefit from enhanced security and accessibility through the use of the Windows Azure Access Control Service.</p></li>
<li><p><strong>Chocolatey</strong> - <a href="http://chocolatey.org/" target="_blank">Chocolatey</a> is moving the solution-wide package manager to new frontiers: NuGet as a system-wide package management tool! Looks promising and is definitely something to keep an eye on.</p></li>
<li><p><strong>Octopus</strong> - <a href="http://www.paulstovell.com/octopus/intro" target="_blank">Octopus</a> is a convention-based solution using NuGet as a protocol for automated deployments. Another example of thinking outside of the box and putting NuGet in a new perspective.</p></li>
<li><p><strong>NuGetFeed</strong> - <a href="http://nugetfeed.org/" target="_blank">NuGetFeed</a> allows you to create a <em>favorites</em> list for NuGet packages, allowing you to monitor them for updates. Great addition to the NuGet ecosystem in my opinion.</p></li>
<li><p><strong>Windows Azure NuGetRole</strong> - Not really a tool, but <a href="http://blog.maartenballiauw.be/post/2011/09/23/NuGet-push-to-Windows-Azure.aspx" target="_blank">a very cool (and working!) concept</a> to push deployments to Azure using NuGet.</p></li>
<li><p><strong>NuGet integration in JetBrains TeamCity</strong> - If you're using TeamCity, <a href="http://blogs.jetbrains.com/teamcity/2011/07/20/nuget-plugin/" target="_blank">you'll get all the NuGet goodness</a> built-in!</p></li>
<li><p><strong>NuGit</strong> - <a href="http://nugit.org/" target="_blank">NuGit</a> is a new kid in the blok: distributing opensource projects faster using NuGet integration with Github</p></li>
<li><p><strong>Artifactory</strong> - <a href="http://www.jfrog.com/news.php?id=42" target="_blank">Artifactory</a> is a well-known repository manager which now also supports NuGet packages.</p></li>
<li><p><strong>Sonatype Nexus Professional</strong> - <a href="http://sonatype.com/Products/Nexus-Professional" target="_blank">Nexus</a> is another repository manager that now embraces NuGet, and they even have a nice <a href="http://www.sonatype.com/people/2012/02/what-is-nuget-for-java-developers/" target="_blank">introduction to NuGet for Java Developers</a> on their blog.</p></li>
<li><p><strong>OpenWrap</strong> - <a href="http://www.openwrap.org/" target="_blank">OpenWrap</a> is an alternative package manager <a href="https://github.com/openrasta/openwrap/wiki/Nuget" target="_blank">compatible with NuGet remotes</a>.</p></li>
<li><p><strong>NuGetMustHaves</strong> - The <a href="http://nugetmusthaves.com" target="_blank">NuGetMustHaves.com</a> website provides a nice categoric overview of popular must-have NuGet packages</p></li>
<li><p><strong>NuGet Server in Java</strong> - <a href="http://blog.jonnyzzz.name/2012/03/nuget-server-in-pure-java.html" target="_blank">This</a> is a Java implementation of a NuGet Server.</p></li>
<li><p><strong>ProGet</strong> - By <a href="http://inedo.com/proget/overview" target="_blank">Inedo</a>, another on-premise NuGet repository tool.</p></li>
<li><p><strong>NuGetFight</strong> - <a href="http://www.nugetfight.com" target="_blank">NuGetFight</a> allows you to enter a NuGet packages battle on a NuGet feed (e.g. NuGet Gallery, Chocolatey, MyGet). Can't decide between two packages? Fight!</p></li>
</ul>

<p>It is great to see how NuGet adoption is growing, especially when people come up with innovative ideas that facilitate our work even further. I'm convinced a package manager such as NuGet should be part of any development environment. If you're not using NuGet yet, consider giving it a try and find out for yourself!</p>

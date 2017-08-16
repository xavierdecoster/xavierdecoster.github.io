---
layout: single
title: "Install NuGet command line extensions using the Package Manager Console"
date: 2011-10-20 20:17:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/10/20/install-nuget-command-line-extensions-using-the-package-manager-console-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2011/10/20/install-nuget-command-line-extensions-using-the-package-manager-console-aspx/.html
 - /2011/10/20/install-nuget-command-line-extensions-using-the-package-manager-console-aspx/.html
---
<p>It's quite late into the night over here, and the blog post is quite abstract, so please bare with me on this one :-)</p>

<p>While I was playing around tweaking and optimizing some things on my recently created <a href="/post/2011/10/07/NuGet-Analyze-now-available-as-command-line-extension.aspx" target="_blank">NuGet.Analyze</a> package, I actually found a way to install a NuGet command line extension from within the NuGet Package Manager Console. Basically, I'm just using some PowerShell scripts to automate the installation procedure.</p>

<p>I've wrapped it up in a package, and created a new cmdlet you all can use from now on: it's called *Install-CommandLineExtension. *To install it, simply install the <strong><a href="http://www.nuget.org/List/Packages/NuGet.InstallCommandLineExtension" target="_blank">NuGet.InstallCommandLineExtension</a></strong> package.</p>

<p><img width="650" height="74" alt="" src="/images/2011-10-20/2011-10.png" /></p>

<p>By default, it will also install a first command line extension that provides the same functionality from the command line. This one adds a new command <em><a href="http://www.nuget.org/List/Packages/AddConsoleExtension" target="_blank">addextension</a></em> to the console. As you can see in the description of the <em>addExtension</em> package, it's instructing you with manual steps to get the thing installed, like this:</p>

<p>To use this package, install the package using NuGet.exe to %LocalAppData%\NuGet\Commands: *nuget.exe install -excludeversion -outputdir %LocalAppData%\NuGet\Commands AddConsoleExtension. *I actually ended up with a similar situation for my own extensions, and didn't feel very comfortable about that. Hence I kept looking for a solution I liked more and came up with this.</p>

<p>So in short: you'll be able to install extensions to the commandline, both from within the commandline as from within the package manager console now, just by installing this one single NuGet package.</p>

<p>Once this new cmdlet is available, you can simply extend you nuget command line as shown below:</p>

<p><img width="650" height="95" alt="" src="/images/2011-10-20/PMC_InstallCommandLineExtension_NuGetAnalyze.PNG" /></p>

<p>If you intend to create your own NuGet Command Line extensions and publish them on the gallery, please use the 'ConsoleExtension' tag for your package. The extensions that are already available do this, and that allows you to very easily find those extensions in the gallery by using the following link: <a href="http://www.nuget.org/List/Search?searchTerm=tag%3A%20ConsoleExtension">http://www.nuget.org/List/Search?searchTerm=tag%3A%20ConsoleExtension</a>. Please give it a try and install any of those console extensions using this new cmdlet.</p>

<p>It just made my NuGet.Analyze.Installer package obsolete, and actually most likely all future installer packages that would have appeared for other extensions as well.</p>

<p>Hope this helps!</p>

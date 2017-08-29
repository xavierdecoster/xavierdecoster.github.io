---
layout: single
title: "Why everyone should be using a symbol server"
date: 2011-11-16 20:24:00 +0100
comments: true
published: true
categories: ["post"]
tags: ["ALM","Community","NuGet","Symbols","Package Management"]
alias: ["/2011/11/16/why-everyone-should-be-using-a-symbol-server/"]
author: Xavier Decoster
redirect_from:
 - /2011/11/16/why-everyone-should-be-using-a-symbol-server/.html
 - /why-everyone-should-be-using-a-symbol-server
---
<p>Today we are excited to <a href="http://blog.maartenballiauw.be/post/2011/11/16/Publishing-symbol-packages-for-a-MyGet-feed.aspx" target="_blank">announce</a> that <a href="http://www.myget.org" target="_blank">MyGet</a> has partnered with <a href="http://www.symbolsource.org" target="_blank">SymbolSource</a> to deliver you private NuGet feeds integrated with private symbol repositories! I really want to send kudos to everyone involved - you know who you are - and I personally really like the direction this is going. With this post I want to explain to you why you, and everyone else, should be using a symbol server as part of his development environment.</p>

<p>First an obvious one: <strong>it is super handy!</strong> We come from an age where we all cloned an open source repository and built the sources locally to reference its resulting binaries. If we were lucky, the binaries where available as well. Others preferred just fetching all sources and reference them as such, so they <em>felt</em> more in control and could see what was happening during the debug process.</p>

<p>With the introduction of a package manager for .NET such as <a href="http://www.nuget.org" target="_blank">NuGet</a>, this behavior has changed. We now can simply query a feed, download and install a package, usually containing binaries which end up as references in the target project. This means that if we now want to step through the code, especially the code from one of those third-party references, we are (actually Visual Studio is) typically missing some information (unless the package author distributes <a href="http://www.wintellect.com/CS/blogs/jrobbins/archive/2009/05/11/pdb-files-what-every-developer-must-know.aspx" target="_blank">PDB files</a> as well, which he really shouldn't).</p>

<p>Before the NuGet-era, those who were using Team Foundation Server (TFS) could easily index there sources in a build definition. As such, a release build, for instance, could easily publish its debug symbols to a central repository. TFS comes with a built-in symbol server, a very neat and little known or used feature. You could simply configure Visual Studio to use the central symbol repository as a <em>symbols source</em> (mind the wording here) for use during the debug process. Read <a href="http://www.edsquared.com/2011/02/12/Source+Server+And+Symbol+Server+Support+In+TFS+2010.aspx" target="_blank">Ed Blankenship's great post</a> on this topic and remember the words: <strong>symbols are as important as source code!</strong> </p>

<p>All this is great! But what if I wasn't using TFS? Well, you could spend a lot of effort and fight your way through <a href="http://msdn.microsoft.com/en-us/library/ms680641%28v=vs.85%29.aspx" target="_blank">Windows Source Server</a> and its scripts for various VCS systems, and a few of us did. It happens to be that some of those brave guys are also the guys that were thinking: is there no easier solution for this? If you're still asking yourself the same question today, now's a good time to read up <a href="http://www.symbolsource.org/Public/Home/About" target="_blank">about SymbolSource</a>, because that's exactly what they meant!</p>

<p>Additionally, there are some <strong>clear benefits for the open source community!</strong> One key benefit for package producers is that it stimulates open source contributions from none team members, or at least allows you to receive more detailed error/bug reports, pointing to the exact location in the code where the issue might be. To all those who do take the effort of reporting those, please do point to source code whenever possible! Any open source project that wants to get better feedback or more contributions/patches/bugfixes from the community, should push his symbols to symbolsource. This brings us to a key benefit for package consumers: you know what€™s happening outside of your code base, because you can now step through and debug in detail, potentially ruling out that the issue is on your side, or on the package€™s side.</p>

<p>It's also <em>*not only for open source code! *</em>Using NuGet and SymbolSource, everyone can now take benefit from a symbol server, whether it concerns symbols of your own or from third parties. If you didn't play with it yet, I really encourage you to do so, and experience the benefits in real life. As of today, it's also no longer limited to open source code: using MyGet and its brand new integration with SymbolSource, you can now very easily set up a private, secured NuGet repository combined with a symbols repository in no time! </p>

<p>All the info you need is right at your fingertips, available in MyGet's feed details. Enjoy!</p>

<p><a href="/images/2011-11-16/image.png"><img src="/images/2011-11-16/image.png" alt="" width="600px" /></a></p>

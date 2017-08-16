---
layout: single
title: "Introducing a NuGet.exe extension for Package Source Discovery"
date: 2013-03-18 00:00:00 +0100
comments: true
published: true
categories: ["post"]
tags: ["NuGet","Package Management","Open source"]
alias: ["/2013/03/18/introducing-a-nuget-exe-extension-for-package-source-discovery/"]
author: Xavier Decoster
redirect_from:
 - /2013/03/18/introducing-a-nuget-exe-extension-for-package-source-discovery/.html
 - /2013/03/18/introducing-a-nuget-exe-extension-for-package-source-discovery/.html
---
<p><p>As we have reached 1500 NuGet feeds on <a href="http://www.myget.org">MyGet</a> for the first time – with some great stuff in our <a href="http://www.myget.org/gallery">Gallery</a> - we felt it's about time we make it even easier for people to discover them. Where NuGet makes it easy to discover packages, we want to push it further and make it easier to discover package sources as well. We've had numerous discussions with several people closely involved with the NuGet project and are quite happy with the outcome of our combined efforts: the NuGet <a href="http://psd.myget.org">Package Source Discovery</a> (PSD) spec. Thanks again <a href="http://www.hanselman.com/" target="_blank">Scott</a>, <a href="http://www.haacked.com" target="_blank">Phil</a>, <a href="http://jeffhandley.com" target="_blank">Jeff</a> and <a href="http://codebetter.com/howarddierking/" target="_blank">Howard</a> for your most valuable input and feedback! Be sure to check the <a href="http://blog.maartenballiauw.be/post/2013/03/18/NuGet-Package-Source-Discovery.aspx">announcement on Maarten's blog</a>!
</p><h1>The Spec
</h1><p>Before you think <em>here we go again with yet another spec</em>, let us explain the following first: we are recycling an existing spec (<a href="https://github.com/danielberlinger/rsd">RSD</a> – Really Simple Discoverability) and reuse existing standards (<a href="http://dublincore.org/documents/2012/06/14/dcmi-terms/?v=elements">Dublin Core</a>). We simply combined them into something suitable for package source discovery. This spec has a single goal in mind: make it as easy as possible to discover package sources (and its metadata).
</p><p>The spec is flexible enough to support very basic as well as more complete implementations:
</p><ul><li>Most basic scenario: define a relationship between your own domain/web site and a NuGet package source by directly pointing to it
</li><li>Most intelligent scenario: define a relationship between your own domain/web site and a NuGet package source by exposing a <em>discovery endpoint</em>
        </li></ul><p>Obviously you can combine the above, or have multiple relationships defined in a single HTML page. We started with a simple NuGet package that adds a new PowerShell cmdlet <a href="http://nuget.org/packages/DiscoverPackageSources/"><em>Discover-PackageSources</em></a> to your NuGet Package Manager console. We also implemented a nuget.exe commandline extension: the <a href="https://github.com/myget/PackageSourceDiscovery/tree/master/src/Extension">NuGet.PackageSourceDiscovery.Extension</a>. Again, all sources are available on our <a href="https://github.com/myget/PackageSourceDiscovery">GitHub repository</a>. Also check the <a href="https://github.com/myget/PackageSourceDiscovery/wiki">project Wiki</a> for further details.
</p><h1>The NuGet CLI Extension
</h1><p style="font-weight:bold;">Important note: this extension requires NuGet.exe v2.3+ (so grab it from <a href="http://build.nuget.org" target="_blank">http://build.nuget.org</a> or wait for the release).</p><p>I usually ship stuff as a NuGet package, however, for this one, I figured it was more appropriate to give you a PowerShell one-liner. Open a CMD prompt and run the following script:
</p><p><pre>@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('http://bit.ly/PSD-ext-install'))"
</pre></p><p>No need to be afraid, the URL this script is pointing to is just a <a href="http://bit.ly/PSD-ext-install">Gist</a> containing some more PowerShell.
</p><p>Once you've installed the extension, it will be available whenever you run nuget.exe on your machine. You can easily verify this by running <em>nuget help</em>. Notice the new <em>discover</em> command?
</p><p><a href="/get/031813_2013_Introducing1_634992344231492908.png" target="_blank"><img src="/get/031813_2013_Introducing1_634992344231492908.png" alt="" style="max-width:750px;"/></a>
    </p><p>Now, let's see how you can use it: <em>nuget help discover</em>.
</p><p><a href="/get/031813_2013_Introducing2_634992344237433220.png" target="_blank"><img src="/get/031813_2013_Introducing2_634992344237433220.png" alt="" style="max-width:750px;"/></a>
    </p><p>Straightforward, isn't it? Let's put it to a test. You probably have guessed by now that my blog now also functions as a NuGet Package Source Discovery endpoint. But first, I want to see the configured NuGet endpoints on my machine: <em>nuget sources. </em>
    </p><p><a href="/get/031813_2013_Introducing3_634992344240090728.png" target="_blank"><img src="/get/031813_2013_Introducing3_634992344240090728.png" alt="" style="max-width:750px;"/></a>
    </p><p>Here goes: <em>nuget discover –Url <a href="">www.xavierdecoster.com</a></em>.
</p><p>The feeds that were discovered are now added to your %appdata%\NuGet\NuGet.config.
</p><p><a href="/get/031813_2013_Introducing4_634992344243373532.png" target="_blank"><img src="/get/031813_2013_Introducing4_634992344243373532.png" alt="" style="max-width:750px;"/></a>
    </p><p>Obviously, we drafted <a href="http://blog.myget.org/post/2013/03/18/Support-for-Package-Source-Discovery-draft.aspx">support for this spec into MyGet</a> as well: read all about it on our blog!
</p><p>What happened under the hood? Check the sources of my blog: you'll find the following tag:
</p><p><pre>&lt;link rel="nuget" type="application/rsd+xml" src="http://nuget.xavierdecoster.com"/&gt;</pre>
</p><p>That URL is a simple 301 to a gist containing my RSD file. I don't even need to redeploy anything to maintain my discovery endpoint: I update the gist and redirect to the new raw gist. Done!
</p><h1>What's next?
</h1><p>Imagine a world where you could point the NuGet Package Explorer to your corporate web site and have it automagically discover the feeds you can use? Or have them added to your NuGet settings in Visual Studio all at once? And when authenticated, it could even discover your API keys and symbol server settings! It's up to you how far you want to take this.
</p><p>One could build a feed monitoring dashboard on top of it, or give me a widget I can plug into my web site, or … well, you get it, use your imagination <span style="font-family:Wingdings">J</span>
    </p><p>We really believe this type of functionality can add value to the NuGet ecosystem, and we'd love to see support for it in all NuGet clients. Send us your feedback, ideas, remarks and concerns. Raise your voice on Twitter, our <a href="http://blog.myget.org">blog</a> and our <a href="https://github.com/myget/PackageSourceDiscovery">GitHub repository</a>. Open an issue, start a discussion, submit a pull request, or even better: let us know about your implementations!</p></p>

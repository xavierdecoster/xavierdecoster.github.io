---
layout: single
title: "MyGet now compatible with NuGet Package Explorer v1.5"
date: 2011-06-15 18:59:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/06/15/myget-now-compatible-with-nuget-package-explorer-v15/"]
author: Xavier Decoster
redirect_from:
 - /2011/06/15/myget-now-compatible-with-nuget-package-explorer-v15/.html
 - /2011/06/15/myget-now-compatible-with-nuget-package-explorer-v15/.html
---
<p>Most of you will agree that <a href="http://nuget.codeplex.com/releases/view/59864" target="_blank">Package Explorer</a> is a major part within the <a href="http://www.nuget.org" target="_blank">NuGet</a> ecosystem. In preparation for the latest version 1.5 release, Luan Nguyen (aka <a href="http://twitter.com/#!/dotnetjunky" target="_blank">dotNetJunky</a>) pointed us to an incompatibility issue with <a href="http://www.myget.org" target="_blank">MyGet</a> (thanks again for that!).</p>

<p>A new package property <strong>IsLatestVersion</strong> was added and Package Explorer depends on it for the improved <em>Select package dialog</em> as <a href="http://npe.codeplex.com/wikipage?title=NuGet%20Package%20Explorer%201.5%20release%20notes" target="_blank">explained here</a>.</p>

<p>I'm glad to announce that MyGet is now using this property as well, with a slight switch to it.</p>

<p>In the NuGet Gallery, a package with IsLatestVersion=true simply means: it is the latest one... duh! :-) for the record: the latest <strong>official</strong> one!</p>

<p>For MyGet, we use this property within the scope of the specific MyGet feed: this means, it is <strong>the latest version available within that specific feed</strong>. Actually, this is exactly the same behavior as within the NuGet Gallery, but the meaning is different because it concerns a private feed on MyGet. The MyGet feed is (currently) unaware of new versions that might get published on the official one. So unless you upload or add a newer package to your MyGet feed, <span style="text-decoration: underline;">the <em>latest version</em> within your private feed might get out-of-sync with the <em>latest version</em> within the NuGet Gallery</span>.</p>

<p>Let me illustrate this with an example: let's say SomeAwesomePackage shipped a first version 1.0 in the NuGet Gallery which you added to your MyGet feed. This will be flagged with IsLatestVersion = true (it's the only version too). You later add a newer version 1.1 of the package, which in turn will be flagged as the latest version, and will unflag the 1.0 version. Now, your happy with this version and didn't upgrade for a couple of months, while version 1.2 has been made available in the NuGet Gallery. At this point in time, your latest version in the MyGet feed (v 1.1) is not the latest version from the NuGet Gallery (v 1.2). Just trying to point out the difference here :-)</p>

<p>To query your feed, just copy/paste your MyGet feed url into the dialog as shown below.</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-06-15/2011-6-npe15testallversions.png" alt="" /></p>

<p>The little checkbox at the bottom saying <em>"Only show latest version of each package Id."</em> is interacting with the IsLatestVersion property of the packages.</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-06-15/2011-6-npe15testlatestversiononly.png" alt="" /></p>

<p>Enjoy!</p>

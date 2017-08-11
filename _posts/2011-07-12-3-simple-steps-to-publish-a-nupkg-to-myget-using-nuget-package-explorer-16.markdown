---
layout: single
title: "3 simple steps to publish a nupkg to MyGet using NuGet Package Explorer 1.6"
date: 2011-07-12 19:27:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/07/12/3-simple-steps-to-publish-a-nupkg-to-myget-using-nuget-package-explorer-16/"]
author: Xavier Decoster
redirect_from:
 - /2011/07/12/3-simple-steps-to-publish-a-nupkg-to-myget-using-nuget-package-explorer-16/.html
 - /2011/07/12/3-simple-steps-to-publish-a-nupkg-to-myget-using-nuget-package-explorer-16/.html
---
<p>Today, Luan Nguyen (<a href="https://twitter.com/#!/dotnetjunky" target="_blank">@dotnetjunky</a>) <a href="http://npe.codeplex.com/wikipage?title=Release%20notes%20for%20NuGet%20Package%20Explorer%201.6" target="_blank">announced the release of NuGet Package Explorer (NPE) version 1.6</a>.</p>

<p>Those of you who installed/updated yet will have noticed he did a great job in improving the look-and-feel of the app, but I wanted to do a shout out here and point you to the feature I'm most excited about.</p>

<p>As you can read in the <a href="http://npe.codeplex.com/wikipage?title=Release%20notes%20for%20NuGet%20Package%20Explorer%201.6" target="_blank">release notes</a>, NPE now <strong>supports publishing packages to a custom source</strong>!</p>

<p>Now how convenient is that for.. let's say.. <a href="http://www.myget.org" target="_blank">MyGet</a> ?! :-)</p>

<p>Here's a little tutorial for you.</p>

<p><strong>How to publish a package to MyGet using Nuget Package Explorer 1.6</strong></p>

<ol>
<li><p>Grab your MyGet feed details (API key &amp; URL)</p>

<p><img width="650" height="501" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-12/2011-07-11_2356.png" /></p>

<p>(Don't try this one, the '<em>Change API key</em>' link <strong>does</strong> work ^^)</p></li>
<li><p>Open up or create your favorite NuGet package in NuGet Package Explorer 1.6</p>

<p><img width="650" height="397" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-12/2011-07-11_2353.png" /></p></li>
<li><p>Click on File > Publish... and follow the instructions using your feed details</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-12/2011-07-12_0010.png" alt="" /></p></li>
</ol>

<p>And that's it! Notice the "<em>package published successfully</em>" message and go check your MyGet feed. The next time you want to publish a package to this feed, Package Explorer will remember your API-key so you can just pick the feed URL from the dropdown. Nice and easy!</p>

<p><img width="650" height="249" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-07-12/2011-07-12_0018.png" /></p>

<p>Note that package details might be cached for a minute on the site, but the feed is updated immediately.</p>

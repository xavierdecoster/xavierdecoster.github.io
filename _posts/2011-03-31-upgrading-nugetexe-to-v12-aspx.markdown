---
layout: single
title: "Upgrading nuget.exe to v1.2"
date: 2011-03-31 17:29:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["NuGet","Package Management"]
alias: ["/2011/03/31/upgrading-nugetexe-to-v12-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2011/03/31/upgrading-nugetexe-to-v12-aspx/.html
 - /upgrading-nugetexe-to-v12
---
<p>Yesterday, <a href="http://haacked.com/archive/2011/03/30/nuget-1-2-released.aspx" target="_blank">Phil Haack announced</a> version 1.2 of NuGet.</p>

<p>Since I already had v1.1 running on my computer, I followed his tip to just run the <em>nuget u</em> command, and you know what, it worked great!</p>

<p>I took a screenshot of the update process and it quickly shows you what new options are available in v1.2.</p>

<p><img src="/images/2011-03-31/2011-3-NuGet_1.1_to_1.2_upgrade.png" alt="NuGet.exe command help" /></p>

<p>David Ebbo already made a great post on the new <em>nuget setApiKey</em> option, which you find <a href="http://blog.davidebbo.com/2011/03/saving-your-api-key-with-nugetexe.html" target="_blank">here</a>.</p>

<p>The <em>nuget spec</em> option allows you to create a new <strong>nuspec</strong> file for a given assembly you can use to build a new package.</p>

<p>Now, let me upgrade my local repository :-)</p>

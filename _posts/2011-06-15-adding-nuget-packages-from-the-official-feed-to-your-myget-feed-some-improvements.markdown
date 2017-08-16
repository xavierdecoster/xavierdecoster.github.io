---
layout: single
title: "Adding NuGet packages from the official feed to your MyGet feed: some improvements"
date: 2011-06-15 18:52:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["NuGet","MyGet","Package Management"]
alias: ["/2011/06/15/adding-nuget-packages-from-the-official-feed-to-your-myget-feed-some-improvements/"]
author: Xavier Decoster
redirect_from:
 - /2011/06/15/adding-nuget-packages-from-the-official-feed-to-your-myget-feed-some-improvements/.html
 - /2011/06/15/adding-nuget-packages-from-the-official-feed-to-your-myget-feed-some-improvements/.html
---
<p>One of the things we want to improve on <a href="http://www.myget.org" target="_blank">MyGet</a> is the add-package functionality from the official <a href="http://www.nuget.org" target="_blank">NuGet</a> feed. We felt this user experience could be better, so here's a first step!</p>

<p>First of all, the <strong>default search behavior</strong> has changed (and we hope improved as well!):</p>

<ul>
<li>the term you enter in the search box is used now to scan the NuGet <strong>package ID and Title only</strong></li>
<li>the default search method is <strong>StartsWith</strong> (self-explanatory I hope?)</li>
<li>uppercasing or lowercasing doesn't matter (we do a ToLower behind the scenes anyway)</li>
<li>by default, we now <strong>only search through the latest versions</strong></li>
</ul>

<p>You'll notice there are a bit more options in the UI as well, so you can adjust the behavior to your needs.</p>

<p><img alt="" src="/images/2011-06-15/2011-6-improvedmygetaddpackagefromnugetfeedpart1.png" width="650" height="304" /></p>

<p>Some of the search settings are now optional:</p>

<ul>
<li>search through the package Summary field</li>
<li>search through the package Description field</li>
<li>search through all versions of all packages</li>
</ul>

<div>
  The moment you type at least two characters, an autocomplete box will display with your matching results, as shown below:
</div>

<p><img src="/images/2011-06-15/2011-6-mygetaddofficialnugetpackageautocomplete.png" alt="" /></p>

<p>In a second phase, we hope to add some more useful functionality to this feature, such as search by Author, OSS license type, ...</p>

<p>Feel free to suggest the ones you feel are really missing on our <a href="https://myget.uservoice.com" target="_blank">MyGet UserVoice</a>.</p>

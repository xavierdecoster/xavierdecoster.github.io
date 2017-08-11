---
layout: post
title: "MyGet tops Vanilla NuGet feeds with a Chocolatey flavor"
date: 2012-03-01 21:06:00 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/03/01/myget-tops-vanilla-nuget-feeds-with-a-chocolatey-flavor/"]
author: Xavier Decoster
redirect_from:
 - /2012/03/01/myget-tops-vanilla-nuget-feeds-with-a-chocolatey-flavor/.html
 - /2012/03/01/myget-tops-vanilla-nuget-feeds-with-a-chocolatey-flavor/.html
---
<p>We recently deployed an all new version of <a href="http://www.myget.org" target="_blank">MyGet.org</a>, which contains quite a lot of optimizations and some new features as well. If you didn't notice, go check it out!</p>

<p>My personal favorite is in fact the underlying architecture that allows us to aggregate feeds and link <a href="http://blog.myget.org/post/2012/03/01/Introducing-MyGet-package-source-proxy-(beta).aspx" target="_blank">package sources</a>. These package source presets are configurable on Feed level through the new *Package Sources *tab available in the Feed management interface.</p>

<div style="display: inline-block;">
  <a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/managePackageSources.png" target="_blank"><img width="650" height="115" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/managePackageSources.png" /></a>
</div>

<p>To add a package from these package source onto your own feed (either referenced or mirrored, with or without its dependencies), navigate to the <em>Add Package *dialog and select the *From Feed</em> tab (previously called "From NuGet.org").</p>

<div style="display: inline-block;">
  <a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/addChocolateyPackage.png" target="_blank"><img width="650" height="189" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/addChocolateyPackage.png" /></a>
</div>

<p>The default setting is still <a href="http://www.nuget.org" target="_blank">NuGet.org</a> obviously, but you might notice the dropdown containing another feed: <a href="http://www.chocolatey.org" target="_blank">Chocolatey.org</a>! That's right, why not add Chocolatey to your package sources and build a feed containing your favorite tools?</p>

<h2>Build your own favorite Chocolatey tools feed</h2>

<p>Building such feed is very straightforward. Choose a feed name (which will be represented in your URL) and provide a meaningful description.</p>

<div style="display: inline-block;">
  <a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/myChocolateyFeedDetails.png" target="_blank"><img width="650" height="218" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/myChocolateyFeedDetails.png" /></a>
</div>

<p>All that is left for a basic MyGet feed is to choose one of the predefined feed templates, as shown below. Obviously you can modify and tweak these settings further to meet your needs afterwards. We've updated our <a title="Frequently Asked Questions" href="http://www.myget.org/site/Faq" target="_blank">FAQ</a> with a full explanation of <a title="MyGet's security model explained" href="http://www.myget.org/site/Faq-Security" target="_blank">MyGet's security model</a> and how you can assign or revoke user rights on a feed.</p>

<div style="display: inline-block;">
  <a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/myChocolateyFeedTemplate.png" target="_blank"><img width="650" height="203" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/myChocolateyFeedTemplate.png" /></a>
</div>

<p>Once the feed is created, you can start pushing packages to it. We support various ways of doing so, including:</p>

<ul>
<li>Uploading packages through the website (and optionally mirror any dependencies found on the configured package source)</li>
<li>Referencing or mirroring packages from another feed (any package source, such as NuGet.org, Chocolatey.org, â€¦)</li>
<li>Uploading a packages.config file, targeting any package source</li>
</ul>

<p>Let's add some packages from the Chocolatey Gallery shall we? Simply select the Chocolatey Gallery package source and type any package name (or any other search criteria using our other search options). Autocomplete will kick in after you typed a character or two and show you a list of possible matches. Select the one you want, verify whether you want to only reference it (copy the metadata onto your feed and keep the real package in the package source) or mirror it (deep copy). If you perform a deep copy, you might prefer to opt-in and check the <em>include dependencies</em> checkbox.</p>

<div style="display: inline-block;">
  <img width="646" height="200" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/addChocolateyPackageGitExtensions.png" />
</div>

<p>After clicking the upload button, the operation is queued into the background for processing. It might take up to a minute until the package (and its dependencies if requested) appear on your feed. Note that we also cache the packages list on the website, but nevertheless, once processed they will appear instantly onto your feed when queried from within Visual Studio for instance.</p>

<p>For this demo, I only added GitExtensions to the feed but I mirrored it and included dependencies. After processing, my feed packages list contains the following packages.</p>

<div style="display: inline-block;">
  <a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/myFavoriteToolsPackagesList.png" target="_blank"><img width="650" height="161" style="border-width: 0px; padding-top: 0px; padding-right: 0px; padding-left: 0px; display: inline; background-image: none;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-03-01/myFavoriteToolsPackagesList.png" /></a>
</div>

<p>Now it's a piece of cake to add those other tools I'm using. You might be wonderingâ€¦</p>

<h2>Why do you want to do that?</h2>

<p>Very simple: I don't want to mess around with a script or packages.config file on multiple computers. If I repaved my system, that file is not on there. If I want to put it on that repaved machine, I need to find it back (that's usually the real issue). And if I somehow manage to do so, it's usually out-of-date.</p>

<p>What if there was a central (read: cloudy) location where I could keep track of this list? A single place to manage the tools list, and always the same location to refer to. Something that would reduce the installation of all my favorite tools to a one-liner. When drafting the Chocolatey chapter in our <a href="http://www.apress.com/9781430241911" target="_blank">Pro NuGet</a> book, I learned about the convenient â€“<em>all</em> command line option support by many of Chocolatey's commands, including the <em>update (cup)</em> and <em>list (clist)</em> commands . That's when it struck me that this switch was missing for the <em>install</em> <em>(cinst)</em> command, so I bothered Rob Reynolds (<a href="http://twitter.com/ferventcoder" target="_blank">@ferventcoder</a>) with it :) Rob was so nice to help us out and made sure the <em>cinst â€“all â€“source</em>Â <em>[feedUrl]</em> scenario would be supported in the near future. Guess what, it is! Thanks again Rob!</p>

<p>Knowing this, it's pretty straightforward to repave a system and get all your favorite tools installed in no time. It suffices to run the following command (replace my feed with your feed):</p>

<div class="wlWriterEditableSmartContent" id="scid:f32c3428-b7e9-4f15-a8ea-c502c7ff2e88:5ec4e8db-8793-439a-9db0-1b5a3641b3ef" style="margin: 0px; display: inline; float: none; padding: 0px;">
  <pre class="brush: bash;">cinst all -source http://www.myget.org/F/myfavoritetools</pre>
</div>

<p>Not only can you use it after repaving your system, you could use it as well every time you work for a new customer and need to set up your development environment (maybe have multiple feeds for various scenarios?), or why not share the feed with your team members and make sure everyone benefits from these awesome tools out there.</p>

<p>If ever you have a question about MyGet or need further assistance to get you started, please refer to our <a title="MyGet Blog" href="http://blog.myget.org" target="_blank">blog</a>, reach out on twitter (<a title="Follow us on Twitter!" href="https://twitter.com/MyGetTeam" target="_blank">@MyGetTeam</a>) or use the <a href="http://www.myget.org/Support" target="_blank">Support</a> form to contact us. We'll be happy to help!</p>

---
layout: post
title: "Team Foundation Services Binaries folder"
date: 2013-01-14 12:17:59 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2013/01/14/team-foundation-services-binaries-folder/"]
author: Xavier Decoster
redirect_from:
 - /2013/01/14/team-foundation-services-binaries-folder/.html
 - /2013/01/14/team-foundation-services-binaries-folder/.html
---
<p>In order to add some custom post-build steps to a TFS Build process, it usually comes in handy - might sound as a surprise - to get a hold on the produced output of that build. If you're familiar with Team Foundation Server and the way it deals with the project output, you know I'm talking about the infamous "Binaries" folder where all produced output gets copied to.</p>

<p>Many problems extending the build process find their origin in this rather annoying convention. Imagine for instance you want to create NuGet packages in a post-build step (<a href="/nuget-tfs-preview-a-challenging-combination">I already told you it's a bit of a challenge</a>). Let's say you want to target certain *.csproj files with an associated *.nuspec manifest, containing metadata that should get merged with the data NuGet is able to derive from the project. Let's say you want to add some extra files (e.g. a readme.txt, some PowerShell scripts...) to the produced NuGet package. Any idea on how the .nuspec file should look like? Any idea where to point the <file src="..."/> element at?</p>

<p>Preferably, you'd like to recreate the package on your development machine as well, for testing purposes, without having to redirect the .nuspec file elements, or any MSBuild property involved in these instructions. This is cumbersome to say the least.</p>

<p>Looking at TFServices, it seems that the Binaries folder got relocated. AFAIK the new location of the Binaries folder is the following one: C:\a\bin. However, I can't find any useful documentation on it. Why the 'a' in the path, should we anticipate a 'b' some day? Why not just C:\bin? What's the underlaying folder structure look like, if any? The Binaries path got shortened, which is a good thing! But a little documentation could 've been nice... (and if I can't find it in any search results on page 1, it's not documented IMO).</p>

<p><strong>Edit:</strong> As Justin points out in a comment to this post, this is considered an implementation detail and as such undocumented as it isn't part of the public contract. The 'a' stands for 'agent'.</p>

<p>So here's a little <a href="https://gist.github.com/4529586">gist</a> I produced to scan the Binaries directory (or any directory for that matter), allowing you to <strong>investigate its contents in the build logs</strong>.</p>

<p>Simply add the following statement anywhere in your MSBuild files where you want the Task to be executed, preferably post-build in the case of the Binaries folder :)</p>

<pre><code>&lt;DisplayBinariesFolder RootFolder="$(OutDir)"/&gt;
</code></pre>

<p>And here's the MSBuild task itself:</p>

<script src="https://gist.github.com/4529586.js"></script>

<p>Hope this helps anyone!</p>

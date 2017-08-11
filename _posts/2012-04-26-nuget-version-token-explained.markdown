---
layout: single
title: "NuGet $version$ token explained"
date: 2012-04-26 21:38:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/04/26/nuget-version-token-explained/"]
author: Xavier Decoster
redirect_from:
 - /2012/04/26/nuget-version-token-explained/.html
 - /2012/04/26/nuget-version-token-explained/.html
---
<p>Many questions that often come to mind when building <a href="http://www.nuget.org" target="_blank">NuGet</a> packages are related to versioning. There's one question in particular I'd like to post here because it's one of the easier to answer. The question is: <strong>How do I use the <em>$version$</em> token in the NuGet manifest (nuspec) file? Where does it get the version number from?</strong></p>

<p>If you look at the <a href="http://docs.nuget.org/docs/reference/nuspec-reference#Replacement_Tokens" target="_blank">NuGet docs explaining the nuspec replacement tokens</a>, it states that the following - if it doesn't, my pull request got accepted :) - <em>The assembly version as specified by the assembly's</em> <strong><em>AssemblyVersionAttribute</em></strong>.</p>

<p>That is not entirely accurate (got a <a href="https://github.com/NuGet/NuGetDocs/pull/27" target="_blank">pull request</a> pending on the docs). Consider the following <em>AssemblyInfo.cs</em> file contents for example.</p>

<pre class="brush: c#;gutter:false;auto-links:false;toolbar:false;wrap-lines:false;">[assembly: AssemblyVersion("1.0.0.0")]
[assembly: AssemblyFileVersion("1.0.0.0")]
[assembly: AssemblyInformationalVersion("1.0.2.0")]</pre>

<p>This is where most people get confused. I won't get into the details of which version attribute you should or should not use, as there can be good reasons to use either one of them in different scenarios. I'll focus on how nuget is using this information to provide a version number to the $version$ replacement token in the nuspec file.</p>

<p>Building a NuGet package using a tokenized nuspec file that relies on assembly information can be achieved in various ways, for instance:</p>

<ol>
<li><em>nuget spec <csproj></em> to generate the tokenized nuspec, followed by <em>nuget pack <csproj></em></li>
<li><em>nuget spec -a <assemblyPath></em> inside the csproj folder to generate a non-tokenized nuspec (so with the metadata already filled in), followed by <em>nuget pack <nuspec></em></li>
</ol>

<p>Basically, it comes down to this:</p>

<ul>
<li>If the <strong><em>AssemblyInformationalVersion</em></strong> attribute is available, then that one is used.</li>
<li>If the <em>AssemblyInformationalVersion</em> attribute is not available, then the <strong><em>AssemblyVersion</em></strong> attribute is used.</li>
<li>If none of the above are specified, your assembly will have a version number of 0.0.0.0, as well as the resulting package.</li>
<li>NuGet totally ignores the <em>AssemblyFileVersion</em> attribute.</li>
</ul>

<p>Note that this behavior is the same when skipping the nuspec at all and building a nuget package directly from an assembly, using <em>using nuget pack <assembly.dll></em>.</p>

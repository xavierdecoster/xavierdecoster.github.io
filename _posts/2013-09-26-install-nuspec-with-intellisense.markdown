---
layout: single
title: "Install-NuSpec with IntelliSense"
date: 2013-09-26 14:33:33 +0200
comments: true
published: true
categories: ["post"]
tags: ["NuGet","Visual Studio","Package Management"]
alias: ["/2013/09/26/install-nuspec-with-intellisense/"]
author: Xavier Decoster
redirect_from:
 - /2013/09/26/install-nuspec-with-intellisense/.html
 - /install-nuspec-with-intellisense
---
<p>If you have quite a few projects you want to get out on NuGet for the first time, it can be a little confusing to find a good starting point. My recommendation usually is to simply create a <a href="http://docs.nuget.org/docs/reference/nuspec-reference#Replacement_Tokens" target="_blank">tokenized</a> NuSpec and package your projects. Target a csproj file, and make sure there's a nuspec file in the same directory with the same name as the csproj file. NuGet will merge the two during package creation.</p>

<p>If you hate manipulating the file system for a single project, imagine doing this for 50 projects. Let's automate this task and preferably without leaving our Visual Studio environment. There's the NuGet Package Manager Console after all.</p>

<p>Well, there you go: <b>Install-Package NuSpec</b><br/>
<a href="https://github.com/myget/NuGetPackages/releases/tag/v3.0.0" target="_blank">Sources on GitHub</a></p>

<p>This package will install itself in the NuGet PowerShell profile, so you can immediately uninstall it, the new cmdlets will still be available. The following new cmdlets are available:</p>

<ul>
<li><b>Install-NuSpec</b> <i>&lt;ProjectName&gt; [-EnableIntelliSense] [-TemplatePath]</i></li>
<li><b>Enable-NuSpecIntelliSense</b></li>
</ul>

<p>Happy Packaging!</p>

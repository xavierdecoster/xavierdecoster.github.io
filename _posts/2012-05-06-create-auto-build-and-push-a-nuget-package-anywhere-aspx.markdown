---
layout: single
title: "Install-NuSpec & Enable-PackagePush: create, build & push NuGet packages anywhere"
date: 2012-05-06 21:46:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/05/06/create-auto-build-and-push-a-nuget-package-anywhere-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2012/05/06/create-auto-build-and-push-a-nuget-package-anywhere-aspx/.html
 - /2012/05/06/create-auto-build-and-push-a-nuget-package-anywhere-aspx/.html
---
<p>This short post is basically combining some of my recent posts (<a href="/post/2012/04/27/Install-Package-NuSpec.aspx" target="_blank">this</a> one and <a href="/post/2012/04/14/Generated-AssemblyVersion-for-NuGet-package-on-TFS-Build.aspx" target="_blank">that</a> one) into one. Actually, into one single NuGet package :) </p>

<p>Basically, this one single package will allow me to automate and speed up package creation and publication, and it will run anywhere you can run the NuGet command line, because I only use nuget.exe and MSBuild to perform these actions. To set it up, I use a NuGet package (Install-Package NuSpec) and some PowerShell, but that's only to put the pieces of the puzzle in the right place, and give you some commands in Visual Studio. Once you're done, simply uninstall the package.</p>

<p>So, in short:</p>

<ol>
<li><strong>Enable Package Restore</strong> to get the .nuget folder (which contains nuget.exe and some nuget.targets file I'll extend)</li>
<li><strong>Install-Package NuSpec</strong> (once per solution, target project doesn't matter)</li>
<li><strong>Install-NuSpec <projectName> [-EnablePackageBuild]</strong> (for every project you want to trigger a package build)</li>
<li><strong>Enable-PackagePush <projectName></strong> (for every project you want to enable auto-push)</li>
<li><strong>Uninstall-Package NuSpec</strong> (once you're finished with steps 1-4 for the solution)</li>
</ol>

<div>
  A more detailed <a href="https://github.com/myget/NuGetPackages/blob/master/README.md" target="_blank">Readme</a> is available on Github (where the <a href="https://github.com/myget/NuGetPackages" target="_blank">sources</a> are if you fancy some PowerShell).
</div>

<div>
  The package can be found on <a href="https://nuget.org/packages/NuSpec/" target="_blank">NuGet</a>.
</div>

<p>If you're using <a href="http://www.myget.org" target="_blank">MyGet</a> already, you simply need to set the <em>feedname</em> and your own personal API-key in the NuGet.Extensions.targets file you'll find under the $(SolutionDir).nuget folder. If you're not using MyGet, simply replace the entire PushPkgSource and SymbolsPkgSource URLs by the one of your choosing.</p>

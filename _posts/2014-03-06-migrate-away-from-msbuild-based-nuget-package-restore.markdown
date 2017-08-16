---
layout: single
title: "Migrate away from MSBuild-based NuGet package restore"
date: 2014-03-06 09:07:46 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2014/03/06/migrate-away-from-msbuild-based-nuget-package-restore/"]
author: Xavier Decoster
redirect_from:
 - /2014/03/06/migrate-away-from-msbuild-based-nuget-package-restore/.html
 - /2014/03/06/migrate-away-from-msbuild-based-nuget-package-restore/.html
---
<h1>Back in the days...</h1>

<p>
NuGet package restore used to be MSBuild-based. You had to explicitly enable it using the context menu on a Visual Studio solution: right-click the solution and select Enable NuGet Package Restore. In fact, if you go to the NuGet docs, you'll see that this scenario is still fully documented. A quick search for "package restore" will throw this old scenario "in your face", as it is the first hit in the search results.</p>

<p><a href="http://docs.nuget.org/search?q=package%20restore" target="_blank"><img alt="First hit in search results when looking for Package Restore on the NuGet docs" src="/images/2014-03-06/searchresults.png" style="max-width:600px;"/></a></p>

<p>
To be fair, the page does highlight that there's a new way of doing this. But many people don't read. At best some look at the pictures. That's why I won't even include a screenshot of that page, as it is full of project setup details that no one should ever go through again. Instead, I'll give you a clear picture of what you should <strong>not</strong> do :)
</p>

<p><a href="/images/2014-03-06/dontdothis.png" target="_blank"><img src="/images/2014-03-06/dontdothis.png" alt="Don't do this!" style="max-height:450px;"/></a></p>

<h1>You're doing it wrong!</h1>

<p>
I can't stress it enough. I'm a huge proponent of NuGet package restore! But if you follow this workflow, then <a href="http://blog.davidebbo.com/2014/01/the-right-way-to-restore-nuget-packages.html" target="_blank">please do it right</a>! (and <a href="http://blog.ploeh.dk/2014/02/03/using-nuget-with-autonomous-repositories" target="_blank">design for failure</a>, obviously).
</p>

<p><p>
The MSBuild-based NuGet package restore has issues. For one: it's MSBuild-based. This means that anything that happens during package restore is run within the MSBuild process, which is particularly annoying for packages that want to modify project files and inject MSBuild targets (as these aren't picked up until the next run).</p><p>The moment you manually enable NuGet package restore through the context menu, you're actually installing a few NuGet packages: <a href="https://www.nuget.org/packages/NuGet.Build/" target="_blank">NuGet.Build</a>, which depends on <a href="https://www.nuget.org/packages/NuGet.CommandLine/" target="_blank">NuGet.Commandline</a>. The <code>nuget.exe</code> along with a <code>nuget.config</code> and a <code>nuget.targets</code> file are created within a <code>.nuget</code> folder, and all projects that have NuGet package references will be modified to import the NuGet.targets file. The nuget.targets file ensures that nuget.exe is invoked <strong>during</strong> builds (as in: <strong>not before</strong> builds!).
</p></p>

<h1>The right way</h1>

<p>All you need to do is to make sure that your Visual Studio options allow NuGet to download any missing packages in a pre-build phase (note: even before MSBuild compilation starts!). I'm not going to rephrase step-by-step what you should do as <a href="http://blog.davidebbo.com/2014/01/the-right-way-to-restore-nuget-packages.html" target="_blank">David Ebbo already has a great post explaining all of this</a>!</p>

<p><img alt="Ensure NuGet is allowed to download missing packages" src="/images/2014-03-06/options.png" style="max-width:600px;"/></p>

<p>If you're cloning a new project that did not commit any NuGet packages (and is not using the old MSBuild-based restore), then it just works!</p>

<h1>Migrating from the old way</h1>

<p>If you still have a <code>.nuget</code> folder in your repository, then <strong>please migrate away from it</strong>! Think about all those adorable kittens...</p>

<p>Did you know this has been <a href="http://docs.nuget.org/docs/workflows/migrating-to-automatic-package-restore" target="_blank">documented on the NuGet Docs</a> all along?! Follow this how-to and save yourself and everyone using your codebase some trouble and follow it step-by-step.</p>

<p>Even better: <a href="https://github.com/owen2/AutomaticPackageRestoreMigrationScript" target="_blank" style="font-weight:bold;">automate your migration using these awesome scripts</a>!</p>

<p>For those not using Visual Studio or perhaps not even on the Windows platform: a simple call to <code>nuget.exe restore *.sln</code> before calling the compiler does the trick as well (add it to your build script, or perhaps you can configure your IDE to do this for you before each build...).</p>

<h1>But... my precious (build server)</h1>

<p>As long as you did not set the environment variable <code>EnableNuGetPackageRestore=false</code> then you're good to go. (default: <code>EnableNuGetPackageRestore=true</code>)</p>

<p>The following tools support the new automatic package restore out-of-the-box and Just Work&#8482;!</p>

<ul>
<li>Anything based on Project Kudu (<a href="/deploying-to-azure-web-sites-using-nuget-package-restore-from-a-secured-feed" target="_blank">Windows Azure Web Sites deployments</a>, Windows Azure Mobile Services C# backend, ...)</li>
<li><a href="http://docs.myget.org/docs/reference/build-services#Package_Restore" target="_blank">MyGet Build Services</a></li>
</ul>

<p>The next list of tools requires some minor modifications to the build process:</p>

<ul>
<li>Visual Studio Online / Team Foundation Server (<a href="http://blogs.msdn.com/b/dotnet/archive/2013/08/27/nuget-package-restore-with-team-foundation-build.aspx" target="_blank">how-to</a>)</li>
<li>TeamCity (<a href="http://blog.jetbrains.com/teamcity/2013/08/nuget-package-restore-with-teamcity/" target="_blank">how-to</a>)</li>
</ul>

<p style="font-weight:bold;">Note that you don't need to worry about development machines! As long as you all have the latest NuGet Visual Studio extension installed (as of NuGet v2.7).</p>

<p>Upgrading your NuGet extension is generally a good idea anyway, as there are lots of improvements in the latest versions!</p>

<h1>Going forward</h1>

<p>
Here's what I'd love to see happen going forward:
<ul>
<li>The NuGet Docs should by default show the new non-MSBuild-based package restore instructions. There are close to none, but this should be thrown in your face when looking for it.</li>
<li>Migration instructions should be clearly linked to.</li>
<li>The old MSBuild-based instructions should be archived, perhaps even removed.</li>
<li>Currently, when installing a NuGet package that injects MSBuild targets, you'll see the following piece of logic appear in your project files:<br/>
<pre>
  &lt;Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild"&gt;
    &lt;PropertyGroup&gt;
      &lt;ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.&lt;/ErrorText&gt;
    &lt;/PropertyGroup&gt;
    &lt;Error Condition="!Exists('..\packages\SomePackageId\Build\SomeMSBuild.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\packages\SomePackageId\Build\SomeMSBuild.targets'))" /&gt;
  &lt;/Target&gt;
</pre>
This is horrible as it is suggesting you to do the wrong thing: you should call <code>nuget.exe restore</code> instead!</li>
<li>The context menu-item to manually enable NuGet package restore (MSBuild-based) should be completely removed from the extension. I don't see any reason to keep it. Do you? If you do, please <a href="https://nuget.codeplex.com/workitem/4019" target="_blank">comment on this CodePlex issue</a>, if you agree, then vote for it :)</li>
<li>Preferably, the next NuGet Visual Studio extension detects you are using the "old" restore option when you open a solution, and asks you to migrate/upgrade to the new way. Ideally, this removes the targets and import statements, and custom package sources and credentials are taken into account if they are in the nuget.config file.
</ul>
</p>

<p>I'm happy to take on an issue or send PR's for any of the above, but some of the bullet-points seem too big to me to be taken in as a PR.</p>

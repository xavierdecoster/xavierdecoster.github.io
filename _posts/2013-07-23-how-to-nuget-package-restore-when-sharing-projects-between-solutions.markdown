---
layout: single
title: "How to: NuGet Package Restore when sharing projects between solutions"
date: 2013-07-23 00:00:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["ALM","NuGet","Package Management"]
alias: ["/2013/07/23/how-to-nuget-package-restore-when-sharing-projects-between-solutions/"]
author: Xavier Decoster
redirect_from:
 - /2013/07/23/how-to-nuget-package-restore-when-sharing-projects-between-solutions/.html
 - /2013/07/23/how-to-nuget-package-restore-when-sharing-projects-between-solutions/.html
---
<p><p>Although it is a situation I try to avoid, it is not uncommon to share project files between solutions. If you are using NuGet package restore on these solutions, and one or more of these shared projects is consuming NuGet packages, you'll likely hit issues. <a href="http://stackoverflow.com/questions/17797052/nuget-not-getting-missing-packages">This StackOverflow question</a> is an illustration of exactly this problem.
</p><h1>Symptoms
</h1><p>Developer A:
</p>

<blockquote><p> "I can't compile Solution2 anymore after pulling the latest version!"
</p>

</blockquote>

<p>Followed by:
</p>

<blockquote><p>"That's strange… it works on my machine." – Developer B
</p>

</blockquote>

<p>Instantly followed by handing out a Pink Sombrero™ to Developer B: (yes, you broke the build! Hopefully…)
</p><p><img src="/get/072313_2304_HowtoNuGetP1_635102175057965339.jpg" alt=""/>
    </p><p>Many teams have lost time debugging this issue, and although I already have quite <a href="/debugging-nuget-package-restore">an exhaustive checklist</a>, this particular issue is not covered as I never really had to deal with this one. I prefer to consume the NuGet package instead of sharing the project…
</p><h1>Example
</h1><p>Behold the following sample folder structure containing 2 solutions. <code>Solution2.sln</code> also references the existing <code>Library1.csproj</code> file (note that it is not inside the solution's root directory):
</p><p><img src="/get/072313_2304_HowtoNuGetP2_635102175062339946.png" alt=""/>
    </p><p>Both solutions have package restore enabled. Solution1.sln has no issues at all.
</p><p><code>Solution2.sln</code> cannot build, because the referenced <code>Library1.csproj</code> project cannot reference its packages. This is tricky: <strong>package restore did work</strong>! But by convention, the packages consumed by <code>Library1.csproj</code> got installed into the solution's Packages folder, which in this case is <code>Solution2\Packages</code>. Package restore (behind the scenes <code>nuget.exe install</code> – horrible command name mismatch) does not <em>install</em> any packages: it downloads and extracts them. None of the package scripts are run, no content is injected, no references added.
</p><p>If you locally have both solutions and you restored the packages consumed by <code>Solution1.sln</code>, then you'll be in the situation of Developer B where <em>it works on your machine</em>. However, Developer A who only opened Solution2 and never built Solution1 will get build failures (as will the build server).
</p><h1>Root Cause
</h1><ol><li>NuGet Package Restore is MSBuild-based at the moment. It is being redesigned so I expect great improvements in the foreseeable future.
</li><li>You're sharing projects and code between solutions. Why not package them instead? Wait, here comes the "debugging experience complex"… It is so convenient to just reference the damn code. I agree within the scope of a <span style="text-decoration:underline">single</span> solution.
</li></ol><p>Sharing between multiple solutions? Here's the deal: set up continuous (package) delivery and use <a href="http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-symbol-package">symbols packages</a> (or a symbols server as the one built-in to TFS). You can use <a href="http://www.symbolsource.org">SymbolSource.org</a> or <a href="/setting-up-your-own-symbolsource-server-step-by-step">set up your own SymbolSource Server</a>. If you need more in order to debug your solution, then frankly you might want to rethink what you're sharing here… This smells like tight coupling and a missed opportunity to share a package.
</p><h1>Patching up the open wound
</h1><p>Although I'd strongly recommend taking a closer look at these projects you're sharing, a short term solution to your problem is also MSBuild-based. You'll need to tweak the package restore command per project. The easiest way to do this is by introducing a new MSBuild property and only deviate from the default conventions when absolutely required. The projects that will require a deviation are those that are being shared obviously.
</p><p>If you disregard the concerns I raised earlier in this post, then here's what I would do to fix this mess (quick-n-dirty style):
</p><ul><li>In <code>Solution1.nuget\nuget.targets</code>, add the following MSBuild property before the <code>&lt;RestoreCommand&gt;</code> element:
</li></ul><p><pre><span style="color:blue">&lt;</span><span style="color:#a31515">PackageRestoreDir </span><span style="color:red">Condition</span>=<span style="color:black">"</span>$(PackageRestoreDir) == ''<span style="color:black">"</span><span style="color:blue">&gt;</span><span style="color:black">$(SolutionDir)\Packages</span><span style="color:blue">&lt;</span>/<span style="color:#a31515">PackageRestoreDir</span><span style="color:blue">&gt;</span></pre></p><ul><li>Within the same file, update the &lt;RestoreCommand&gt; by appending the following:
</li></ul><p><pre><span style="color:blue">&lt;</span><span style="color:#a31515">RestoreCommand<span style="color:blue">&gt;</span><span style="color:black">$(NuGetCommand) install … -o "$(PackageRestoreDir) "<span style="color:blue">&lt;</span>/</span><span style="color:#a31515">RestoreCommand<span style="color:blue">&gt;</span></span></span></pre></p><ul><li>In the Library1.csproj file, add the following MSBuild property (mind the order of precedence with &lt;SolutionDir&gt;):
</li></ul><p><pre><span style="color:blue">&lt;</span><span style="color:#a31515">SolutionDir </span><span style="color:red">Condition</span>=<span style="color:black">"</span>$(SolutionDir) == '' Or $(SolutionDir) == '<em>Undefined</em>'<span style="color:black">"</span><span style="color:blue">&gt;</span><span style="color:black">..\</span><span style="color:blue">&lt;</span>/<span style="color:#a31515">SolutionDir</span><span style="color:blue">&gt;</span><span style="color:black"></span><br/><span style="color:blue">&lt;</span><span style="color:#a31515">RestorePackages</span><span style="color:blue">&gt;</span><span style="color:black">true</span><span style="color:blue">&lt;</span>/<span style="color:#a31515">RestorePackages</span><span style="color:blue">&gt;</span><span style="color:black"></span><br/><span style="color:blue">&lt;</span><span style="color:#a31515">PackageRestoreDir</span><span style="color:blue">&gt;</span><span style="color:black">$(SolutionDir)..\Libraries\Packages</span><span style="color:blue">&lt;</span>/<span style="color:#a31515">PackageRestoreDir</span><span style="color:blue">&gt;</span></pre></p><p>Repeat the last step for each existing shared project you referenced in your second solution. The <code>&lt;PackageRestoreDir&gt;</code> should match the project reference and the default package restore directory of the main solution of that project.</p><p>You can download a sample solution <a href="/images/2013-07-24/multisolutionroot.zip">here</a>.
</p><p>Happy packaging!</p></p>

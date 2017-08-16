---
layout: single
title: "Debugging NuGet Package Restore"
date: 2012-08-21 22:18:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["NuGet","Package Management"]
alias: ["/2012/08/21/debugging-nuget-package-restore/"]
author: Xavier Decoster
redirect_from:
 - /2012/08/21/debugging-nuget-package-restore/.html
 - /2012/08/21/debugging-nuget-package-restore/.html
---
<h2>Package Restore Internals</h2>

<p>When enabling NuGet package restore, the NuGet Visual Studio extension modifies your projects in the following way:</p>

<ul>
<li>A .nuget folder is created next to your solution, containing MSBuild targets and the nuget.exe command line tool.</li>
<li>Your existing projects that already consume NuGet packages are modified: the <restorePackages> MSBuild element is added and set to True, and an <import> statement is configured to import the NuGet MSbuild targets. (and technically, a <solutionDir> property is set as well, allowing you to configure the location of your solution if you deviate from the default convention of having a projectdir for each project in your solution: $(SolutionDir)\ProjectDirA\ProjectA.csproj)</li>
</ul>

<p>Check the NuGet documentation to <a href="http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages" target="_blank">learn how to set up NuGet package restore</a>. I'll focus on the issues now.</p>

<h2>Debugging Checklist</h2>

<p>Depending on the symptoms of your issue, you can start investigating locally, or working your way back starting from the build server. Obviously, the easiest way is to check if all runs fine locally.</p>

<ul>
<li>Clear your $(SolutionDir)\Packages folder (except for the repositories.config file)</li>
<li>Clean your solution (delete bin &amp; obj folders)</li>
<li>Clear your local machine NuGet cache (%LocalAppData%\NuGet\Cache)</li>
</ul>

<p>Rebuild your solution and check if everything compiles OK and the correct packages are installed.</p>

<h3>In Visual Studio</h3>

<ul>
<li>By default, NuGet Package Restore enables restore only for those projects that already consume NuGet packages. If you first enabled package restore and then started consuming packages, it is possible that some projects aren't configured properly. You can verify this by checking whether the project files contain the <restorePackages>true</restorePackages> MSBuild property.</li>
<li>NuGet Package Restore assumes you have a solution in a root directory $(SolutionDir) and all projects have their own subdirectory (e.g. $(SolutionDir)\ProjectDirA\ProjectA.csproj). If you deviate from this convention, you have to manipulate the <solutionDir> MSBuild element in the project files accordingly (using relative paths!).</li>
<li>Ensure the nuget.targets file <packageSources> element is pointing to the correct URL. You can define multiple package sources using a semicolon separator. The order in which they are defined is also the order in which NuGet will look for your packages. When using paths that contain spaces, also ensure to put double quotes around the entire property value declaration (e.g. <packageSources>"\some unc\path;https://some url/feed/packages"</packageSources>)</li>
<li>If you upgraded some regular references to NuGet package dependencies, ensure the <hintpath> in the project file for that reference is pointing towards the correct location in the packages folder.</li>
</ul>

<h3>In Version Control</h3>

<ul>
<li>No matter what type of VCS you are using, NuGet Package Restore requires the .nuget folder to be checked-in, including the nuget.targets, nuget.config and nuget.exe.</li>
<li>Double-check whether you are having the actual nuget.exe command line in the .nuget folder, and not the bootstrapper. The commandline is around 600Kb in file size, the bootstrapper 15Kb. The bootstrapper requires internet access, and has been made obsolete now. It might be revived in the future, but for now, you should make sure you use the nuget.exe command line tool instead. You can get the nuget.exe command line manually as well by downloading it <a href="http://nuget.org/api/v2/package/NuGet.CommandLine/2.0.0" target="_blank">here</a>. Unzip it and replace your 18Kb nuget.exe by the one you'll find inside the package.</li>
<li>Ensure you checked in the Packages\repositories.config file. When using TFS, you'll be better off <a href="/post/2011/10/17/Tell-TFS-not-to-add-NuGet-packages-to-source-control.html" target="_blank">when defining appropriate workspace mappings</a> for this.</li>
</ul>

<h3>On The Build Server</h3>

<ul>
<li>NuGet Package Restore requires a consent (due to privacy policy &amp; legal requirements). This can be set using a system-wide environment variable, which is <a href="http://blog.nuget.org/20120518/package-restore-and-consent.html" target="_blank">explained on the NuGet Blog</a>.</li>
<li><p>When using a secured feed which requires you to authenticate before consuming anything, you'll have to configure the required credentials in the NuGet.config. Ensure you are running this under the build service account. If the service account cannot remote desktop into the server but you can, then you might run a command prompt under another user by running the following from the command line:
<pre>runas /user:DomainName\TfsBuildServiceAccountName cmd
</pre> You'll be prompted for the password and a new command prompt should open. In this new command prompt, follow the below instructions to save authentication information for a given package source. </p>

<p><pre class="brush: plain; gutter: true; first-line: 1; tab-size: 2;  toolbar: true;">nuget.exe sources add -name [feedName] -source [feedUrl]
nuget.exe setapikey [apikey] -source [feedUrl]
nuget.exe sources update -Name [feedName -User [username] -pass [password]
</pre> When using <a href="http://www.myget.org" target="_blank">MyGet</a>, we recommend you to use/create a specific user account for the build service account, if only to avoid you breaking all builds when changing your own password or apikey :)</p></li>
</ul>

<p>Hope this helps anyone!</p>

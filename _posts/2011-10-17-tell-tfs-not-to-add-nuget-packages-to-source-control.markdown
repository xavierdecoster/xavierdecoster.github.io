---
layout: single
title: "Prevent TFS from adding installed NuGet packages to source control"
date: 2011-10-17 20:09:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/10/17/tell-tfs-not-to-add-nuget-packages-to-source-control/"]
author: Xavier Decoster
redirect_from:
 - /2011/10/17/tell-tfs-not-to-add-nuget-packages-to-source-control/.html
 - /2011/10/17/tell-tfs-not-to-add-nuget-packages-to-source-control/.html
---
<p>I recently came accross a tweet asking for help, because TFS was always adding the installed NuGet package binaries into source control automatically. Especially when you want to use <a href="http://www.nuget.org" target="_blank">NuGet</a> with <a href="/post/2011/07/18/Continuous-Package-Integration-NuGet-vs-Source-Control.aspx" target="_blank">a no-commit strategy</a>, this can be fairly annoying. I also realize a DVCS is using the concept of a .ignore file or something similar, which I really like, but this is not the concept TFS uses. So for those on TFS source control, here's my workaround...</p>

<p>In a nutshell, all we want to have in our VCS, is a trace of which dependencies we have, and which versions we depend on. So in NuGet terms, we're talking about package IDs and package versions. This happens to be exactly what is being stored into the <em>packages.config</em> files that NuGet produces when managing NuGet packages in a project/solution. Next to that, there's also the <em>repositories.config</em>, by default located under the <em>$(solutionDir)\packages</em> folder. The repositories.config, as the file name suggests, keeps track of the different NuGet repositories that your solution projects rely on and is thus pointing to all packages.config files for a given solution. This is the kind of meta-information we want to keep track of.</p>

<p>The no-commit strategy tells you not to commit any NuGet packages into your VCS, but as most people using TFS have mapped the entire branch to their workspace, this causes issues. Whenever we install a package through VisualStudio into a mapped workspace folder, VisualStudio tells TFS to add these files to source control. We want to keep track of the repositories.config, but not of the actual packages, which are by default being installed into the same $(solutionDir)\packages folder. Respecting NuGet's <em>convention-over-configuration</em>Â approach, we want to keep these defaults.</p>

<p>My workaround is actually pretty simple, albeit not that straightforward given the lousy UI that the workspace mapping dialog provides. If you thought you could only map folders, then think again! :-) You can map single files as well!</p>

<p>Knowing this is half of the work, the other part is just setting the correct workspace mapping, as shown below:</p>

<p><a href="/images/2011-10-17/tfs_workspace_nuget_packages_folder.png" target="_blank"><img width="650" height="338" alt="" src="/images/2011-10-17/tfs_workspace_nuget_packages_folder.png" /></a></p>

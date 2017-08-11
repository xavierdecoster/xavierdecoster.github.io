---
layout: post
title: "Generate NuGet package dependency matrix directly from TFS source control"
date: 2011-10-06 19:57:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/10/06/generate-package-dependency-matrix-directly-from-tfs-source-control-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2011/10/06/generate-package-dependency-matrix-directly-from-tfs-source-control-aspx/.html
 - /2011/10/06/generate-package-dependency-matrix-directly-from-tfs-source-control-aspx/.html
---
<p>I've played with this idea for a while now, and I've finally <a href="http://nuget.codeplex.com/SourceControl/network/Forks/XavierDecoster/NuGetTfs" target="_blank">forked the NuGet sources</a> to add this functionality to the Commandline tool. This is work in progress, so please bear with me on this one. If you find defects or have suggestions for improvement, feel free to drop me a mail or comment on this post ;-)</p>

<p>Something any decent software factory wants to know at any time is the answer to the following questions:</p>

<ul>
<li>Who is using version X of component Y?</li>
<li>Which versions of component Z are in use (and which not)?</li>
<li>What dependencies does application A have, and to which versions?</li>
</ul>

<p>Answering these questions typically requires someone to maintain a spreadsheet with dependency matrixes. My goal is to get rid of this work and automate this. I've used TFS Source Control for my implementation as I can probably find immediate use for it. Also, it was a good exercise to play with the <a href="http://msdn.microsoft.com/en-us/library/bb130146(v=VS.100).aspx" target="_blank">Team Foundation Server 2010 SDK</a>.</p>

<p>Here's a screenshot of what I committed into my NuGet fork:</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-10-06/nugetcmdlineanalyse.png" alt="" /></p>

<p>Why did I add the <em>tfs</em> action to the command? Because I'd love to see this command work for other version control systems as well, such as SVN, Git, Mercurial...</p>

<p>I've obviously still got some work to do on the authentication part as well as you might notice, although I use <a href="http://msdn.microsoft.com/en-us/library/microsoft.teamfoundation.client.uicredentialsprovider(v=VS.100).aspx" target="_blank">UICredentialsProvider</a> from the SDK, which should prompt you with a dialog when the system cannot automatically authenticate you, as shown below.</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2011-10-06/nugetcmdlineanalyseauthentication.png" alt="" /></p>

<p>As you can see in the first screenshot of this post, I currently enumerate the entire server for all Team Project Collections and all Team Projects within them. The command will of course respect source control permissions for your account and only return those you have access to. Within each Team Project, a lookup is done for any repositories.config and packages.config files, their XML is analysed and a dependency matrix is generated for each Team Project.</p>

<p>Possible improvements I can think off are:</p>

<ul>
<li>add a <em>-Repository</em> option to the command to limit the scope of the analysis on the server to a specific path (and everything underneath)</li>
<li>add some cmdline authentication options</li>
<li>fine tune the analysis on the branch and/or solution level instead of on the team project level</li>
<li>making this a commandline extension command (instead of built-in) to avoid dependencies to TFS SDK in NuGet cmdline?</li>
</ul>

<p>I'm sure you might have other great ideas as well, so don't hesitate to post them here, or provide me with a patch :-)</p>

<p>This is a first implementation and needs further testing, so you've been warned! Hold on to your <a href="http://en.wikipedia.org/wiki/Neutrino" target="_blank">neutrinos</a> if things go horribly wrong!</p>

<p>Enjoy!</p>

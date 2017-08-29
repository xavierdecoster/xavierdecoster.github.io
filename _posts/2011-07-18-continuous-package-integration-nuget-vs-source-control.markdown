---
layout: single
title: "Continuous Package Integration: NuGet vs Source Control"
date: 2011-07-18 19:32:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["ALM","NuGet","Package Management"]
alias: ["/2011/07/18/continuous-package-integration-nuget-vs-source-control/"]
author: Xavier Decoster
redirect_from:
 - /2011/07/18/continuous-package-integration-nuget-vs-source-control/.html
 - /continuous-package-integration-nuget-vs-source-control
---
<p><strong>Update (August 17, 2011):</strong></p>

<p><strong><em><a href="http://twitter.com/#!/davidfowl" target="_blank">David Fowler</a> created an awesome <a href="https://github.com/davidfowl/NuGetPowerTools" target="_blank">NuGetPowerTools</a> package that streamlines this process further. Also <a href="http://blog.davidebbo.com/2011/08/easy-way-to-set-up-nuget-to-restore.html" target="_blank">check out David Ebbo's post</a> for more info.</em></strong></p>

<p>One of the questions I receive regularly when talking about enterprise approaches for using <a href="http://www.nuget.org" target="_blank">NuGet</a>, is the following one:</p>

<p><em>"Why don't you put the NuGet packages in source control as well?"</em></p>

<p>In my opinion a very valid question to ask, and I reached the point where I realized it's better to write a blogpost on it once and use it for future reference :-)</p>

<p>Please note: what I will discuss here is my take on it, and not the holy grail!</p>

<h3>Storing NuGet packages in source control?</h3>

<p>As <a href="http://blog.davidebbo.com/2011/03/using-nuget-without-committing-packages.html" target="_blank">David Ebbo already explained</a>, there are approaches to not store the NuGet packages in source control. Your question is: why?</p>

<p>One could say that disk space is cheap! That's a valid approach if you have no issues repeating your dependencies in source control every single time. I'd like to see however how that works out for you if you have let's say 500 line of business apps in your SCM, all having the same dependency to your favorite logging library of around 0.5Mb. That's 250Mb worth of storage! This also slows down the time needed to perform a backup, or the disk space required to store your backups, thus increasing your costs in storage and power consumption. Now extrapolate this reasoning to all your dependencies..</p>

<p>Add on top of this that some SCM's don't manage binary files that well (TFS anyone?): ask your devs how much time they lost already fighting with a system that is unable to do binary diffing? You ever experienced upgrading an assembly in TFS from version X to version Y? Forgot to explicitly check out the file, replaced it on disk, to find out the system tells you it found no changes to commit? Don't get me wrong, TFS is a great tool, but its source control system could be better.</p>

<p>For some, this is a reason not to use TFS, but I'd say that any Source Control system is meant to store <span style="font-weight: bold; text-decoration: underline;">sources</span>, <strong>not binaries</strong>. Use a document management system if you need document versioning and take benefit from its search features for instance. Assemblies explicitly tell you their AssemblyVersion, so why would you want history on those files?</p>

<p>What you really want to is to have <strong>history on your dependencies</strong>! You want to be able to say: version A of my app depends on version X of a dependency, version B of my app depends on version Y of that same dependency, we upgraded X to Y on that date when person P was working on feature F... That's the interesting information, not the actual binary.</p>

<p>Now people argue: <em>Yeah, but I want my builds to be reproducable, a given changeset *(pardon my wording: *revision</em>)* should always produce the same output.* Agreed! But does that mean that the dependencies over which you don't have any control need to be in source control? You know, those libraries that already are versioned, the information already compiled and baked into the binary file? Nope, just reference them, you'll only find one binary that matches that specific version of the library out there (I'm coming back on this point later in this post). Now that's exactly the kind of information you'll find in the <em>repositories.config</em> and <em>packages.config</em> files that NuGet uses to keep track of your package dependencies.</p>

<p>A benefit of storing the packages in source control is that its consumers don't need to rely on NuGet. This is one of the main principles behind NuGet, as <a href="http://haacked.com/archive/2010/10/06/introducing-nupack-package-manager.aspx" target="_blank">outlined by Phill Haack in the announcement of NuGet</a>. That is nice indeed, and it is cool that you <em>can</em> do that, but should you? There are cases where you should, but I find them rare. The main reason for me to store those packages in source control would be because you don't want to wait for that choice to be made :-) Team A kicking off a new project could start using it, while Team B has no time because it's dealing with <a href="http://en.wikipedia.org/wiki/Feature_creep" target="_blank">featuritis</a>. In such situations you could benefit from storing the packages to avoid impacting other teams. </p>

<p>In my opinion, using NuGet or supporting teams that want to use it, <em>should preferably be</em> a <strong>company-wide strategic choice</strong>. For the same reason, you probably won't mix IDEs (Visual Studio, SharpDevelop, Notepad, VIM, other? :-)) amongst your team members, or mix source control systems (TFS, Git, SVN, ...), build servers, ...</p>

<p>So here is my statement:</p>

<ul>
<li><strong>don't store the binaries in source control</strong>: keep NuGet packages out</li>
<li><strong>only store the metadata in source control</strong>: keep track of repositories.config and packages.config files</li>
<li><strong>have a strategy</strong>: standardising the way you embrace the power of NuGet inside the organisation will benefit you all and avoid headaches</li>
</ul>

<h3>Continuous (Package) Integration</h3>

<p>Now, as stated earlier: every build of a given changeset/revision of your project should always produce the same output. How does this work when you depend on files which are not in source control?</p>

<p>Given the fact you do store what you depend on (<em>metadata</em>), all you need to do is fetch those dependencies in a pre-build step. This is what I call <strong>Continuous Package Integration</strong>.</p>

<p><em>Note: I'm speaking about CI builds here by example. I use the exact same approach for any other type of build: QA builds, Release builds, ...</em></p>

<p><img width="650" height="392" alt="" src="/images/2011-07-18/cpi.png" /></p>

<p>This setup implies you have a corporate NuGet server running, preferably even with multiple feeds. There are multiple ways to accomplish this, but if you want a quick start and play with this setup, I suggest you try out <a href="http://www.myget.org" target="_blank">MyGet (NuGet-as-a-Service)</a> and focus on your process first. MyGet can also help in <a href="http://blog.maartenballiauw.be/post/2011/07/15/Copy-packages-from-one-NuGet-feed-to-another.aspx" target="_blank">ensuring a specific package version is always available by mirroring packages from the official NuGet feed</a>.</p>

<h3>Optimization</h3>

<p>Now, it might seem cumbersome to continuously fetch those packages every single time you do a build, but what prevents you of setting up a caching mechanism on your build agents? Now its my turn to tell you disk space is cheap :-) (with the addition that it's used as a local cache: no need for backup).</p>

<p>This could be very simply accomplished with a few PowerShell scripts hooking into the build steps, that check the local cache first, and if not present fetch it. This mechanism will be self-organizing and become faster as we use it (don't you like that?).</p>

<p>The same counts for your development machines: have a script and local cache for the packages you use. Run a pre-build that checks if any repositories.config or packages.config file has changed and updates your local environment.</p>

<p>Please, feel free to comment and share your thoughts on this matter!</p>

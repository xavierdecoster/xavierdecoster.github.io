---
layout: single
title: "NuGet & TFS Preview: a challenging combination"
date: 2012-10-25 22:22:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["ALM","NuGet","TFS","Package Management"]
alias: ["/2012/10/25/nuget-tfs-preview-a-challenging-combination/"]
author: Xavier Decoster
redirect_from:
 - /2012/10/25/nuget-tfs-preview-a-challenging-combination/.html
 - /nuget-tfs-preview-a-challenging-combination
---
<p>I've recently spent some time trying to come up with an end-to-end demo of using <a href="http://www.nuget.org" target="_blank">NuGet</a> on <a href="http://www.tfspreview.com" target="_blank">TFS Preview</a>, and I wanted to share you my findings.</p>

<p>My scenario looks as follows:</p>

<ul>
<li>NuGet packages are <a href="http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages" target="_blank">restored</a> from an&nbsp;<strong>HTTPS</strong> endpoint on <a href="http://www.myget.org" target="_blank">MyGet.org</a></li>
<li>The build agent auto-increments the build number and check-in the changes to the AssemblyInfo.cs file</li>
<li>Compilation</li>
<li>NuGet packages are created using this incremented version</li>
<li>NuGet packages are pushed to an HTTPS feed on MyGet.org</li>
</ul>

<p>This is quite a basic Continuous Integration set-up which <a href="http://www.developerfusion.com/article/144809/continuous-integration-using-nuget-and-teamcity/" target="_blank">has worked for me before on TeamCity</a>. However, on TFS Preview I would soon find out it's not as straightforward as one would think it is...</p>

<p>If we try to visualize the above scenario, you'd end up with the following flow:</p>

<p><img alt="" src="/images/2012-10-25/end2end.png" width="750" height="200" /></p>

<p>Now how to implement this? I didn't want to customize any build definition templates for a multitude of reasons, but let's stick to the fact I didn't want to :) That leaves me with few other alternatives: MSBuild, PowerShell and NuGet.exe. I've been in this situation before and was hoping to be able to reuse some of the stuff I used on <a href="http://www.jetbrains.com/teamcity/" target="_blank">TeamCity</a>. So even if you don't use TFS or TFS Preview at all, I hope you'll find some useful information in this post.</p>

<h2>Expect some challenges</h2>

<p>First things first, let's start with the HTTPS endpoint from which the build agent should restore NuGet packages in a pre-build step. When enabling the NuGet package restore feature on your solution, the <a href="http://haacked.com/archive/2012/10/23/the-truth-about-nuget-and-its-future.aspx" target="_blank"><em>NuGet-based Microsoft Package Manager</em></a> (aka NuGet VSIX, aka NuGet Visual Studio Extension) is creating a .nuget folder in your solution directory, injects some files and modifies some if not all of your project files. The files that are being fetched are: nuget.config, nuget.exe and the nuget.targets MSBuild file. Enabling package restore is an optimal experience with little hassle (disabling however isn't). Now where is the catch?</p>

<p>Package restore actually is a call to&nbsp;<em>nuget.exe install</em> with some parameters. One of them is the&nbsp;<em>-source</em> option, allowing you to define which package source should be used to fetch packages from. If the -source option points to an HTTPS endpoint, guess what, you'll be <strong>prompted</strong> for credentials. So we need to feed the tool with the required credentials in a non-interactive way. Luckily enough, there's an option for this as well: simply add the&nbsp;<strong><a href="http://docs.nuget.org/docs/reference/command-line-reference#Options" target="_blank">-NonInteractive</a></strong> switch. This will instruct the NuGet command line tool to silently look for credentials in the nuget.config file. Perfect, right?</p>

<p>NuGet v2.1 is now supporting <a href="http://docs.nuget.org/docs/release-notes/nuget-2.1#Hierarchical_Nuget.config" target="_blank">hierarchical configurations</a>, so adding these credentials to our solution-level nuget.config should work. At least, that's what I thought... Turns out it doesn't take into account credentials yet (I logged an <a href="http://nuget.codeplex.com/workitem/2749" target="_blank">issue</a>). However, I know that nuget.exe does check the nuget.config in the build account's roaming user profile... IF you use the nuget.exe from that location, which doesn't exist, so I have to copy it first. You can see things can quickly get complicated here: a NuGet bug (?) requires me to first <strong>copy over nuget.exe to the %AppData%\NuGet folder</strong> (which I also must create), after which I also need to run a nuget.exe command to <strong>register the package source and its credentials</strong>... And all this must happen&nbsp;<em>before</em> package restore even kicks in. Obviously, that would also mean you need to do some cleanup at the end of the build process as well, unless you run on TFS Preview (because builds run in a sandboxed environment).</p>

<p>All of this can easily be done using a few MSBuild instructions and nuget.exe commands, and this is how I found out that the build agent seems to be running some exotic language pack on the OS :)</p>

<p><strong>Edit:&nbsp;</strong>Actually, it's not. As Chris Patterson explains in a comment to this post, this was bug for which a fix will be deployed soon.</p>

<p>Here's the path to the build account's roaming profile directory: C:\Users\畢汩杤敵瑳\AppData\Roaming. I remember the build user is named&nbsp;<em>buildguest</em>, but because of these exotic characters I occasionally had to switch between %appdata% and $(APPDATA) in my MSBuild file.</p>

<h2>Restore works, next up: versioning</h2>

<p>I've been doing <strong>auto-versioning</strong> before using the MSBuild Extension Pack which contains an AssemblyVersionTask. However, I also noticed that this task's changes to the AssemblyInfo.cs file are being undone! This happens inside the DLL that is containing this MSBuild Task, so I created my own <em>NuGet.MSBuildExtensions.dll</em> (currently very basic and only fixing this scenario). Undoing changes is not desirable, as we need to keep track of the version change. This means that these changes need to be <strong>committed into source control</strong>. To do that in a&nbsp;<em>non-interactive</em> way, you need to use <a href="http://msdn.microsoft.com/en-us/library/cc31bk2e(v=vs.110).aspx" target="_blank">tf.exe</a> and know the credentials of the account to be used to check-in these changes. Another challenge: what are my TFS Preview credentials? I use a Windows Live ID for authentication, so should I simply use my email and password? Nope, turns out there's a <a href="http://blog.hinshelwood.com/tfs-service-credential-viewer/" target="_blank">TFS Service Credential Viewer</a> which allows you to authenticate using your Windows Live ID and fetch your actual username and encrypted password. This is what you should be using when checking in those changes using tf.exe.</p>

<h2>Easy, let's create packages</h2>

<p>Here's another breaking change coming into play: those familiar with TFS know that all TFS output by default ends up into a&nbsp;<em><strong>Binaries</strong></em> folder, which is a sibling folder of the&nbsp;<em>Sources</em> folder where your Build Definition workspace gets checked out. Big news: this folder <strong>got renamed to</strong>&nbsp;<strong>bin</strong>! Again, I found out by trial and error... This also means you need to ensure that your project output ends up in this bin folder for the Release configuration (assuming you are building in Release mode). Simply change this in your project's properties: replace <i>"bin\Release"</i> by <em>"..\..\bin\"</em>, and do this for each project that should produce a NuGet package. You can optionally create a NuSpec file and make it available next to your project file to add some additional package metadata.</p>

<h2>Actual scenario</h2>

<p>All of the above makes it very cumbersome to support this scenario on TFS Preview at the moment. I'm sure if you'd go the custom build definition template way you'd end up with something very similar but less portable. I prefer this MSBuild + command line approach because it allows me to partially reuse some scripts on another environment such as TeamCity, which definitely has the lead at the moment in supporting NuGet in Continuous Integration scenarios. Adding the above "hacks" to the visualization gives us the following image:</p>

<p><img alt="" src="/images/2012-10-25/end2end-detailed.png" width="750" height="285" /></p>

<p>I've <a title="Click to download" href="/images/2012-10-25/.nuget.zip" target="_blank">uploaded</a> the relevant sources to this post for those interested. I'd rather see first-class support for NuGet rather soon (meaning full support for this scenario as a bare minimum)...</p>

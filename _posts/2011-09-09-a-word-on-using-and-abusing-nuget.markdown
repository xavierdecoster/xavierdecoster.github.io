---
layout: single
title: "A word on using and abusing NuGet"
date: 2011-09-09 19:37:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/09/09/a-word-on-using-and-abusing-nuget/"]
author: Xavier Decoster
redirect_from:
 - /2011/09/09/a-word-on-using-and-abusing-nuget/.html
 - /2011/09/09/a-word-on-using-and-abusing-nuget/.html
---
<p>One thing I really like about using NuGet is the fact that it creates a window of opportunities: you can use it in different ways, you can abuse it in different ways. Whether you use it, or you abuse it (read: use it in a different way than it was intended to be used), that's totally up to you. However, there are some common pitfalls I've noticed people struggling with when taking things to the next level.</p>

<p>NuGet is a package management system: it facilitates finding, installing, updating and removing NuGet packages. It also manages metadata such as version and dependency information to prevent missing prerequisites. Packages typically contain binaries and thus implies, in most people's heads, an added dependency when installing the package in a target project or solution. This is as basic as NuGet can get.</p>

<h3>What you see is what you get?</h3>

<p>Some very smart guys immediately saw an opportunity here: what if my package is adding <em>functionality</em> instead of a <em>dependency</em>? Hence <a href="http://blog.stevensanderson.com/2011/01/13/scaffold-your-aspnet-mvc-3-project-with-the-mvcscaffolding-package/" target="_blank">scaffolding</a> was born: really great stuff! This subtle but important tweak kills the assumption though that an installed NuGet package is an added dependency.</p>

<p><strong>Conclusion #1: Installing a NuGet package does not necessarily add a software dependency</strong> to the target project/solution you're developing on.</p>

<p>It usually does, but do not assume it does it all the time. The importance of this first conclusion might not be obvious at first, but I hope to explain it by the end of this post, when you've read my second conclusion.</p>

<h3>Don't automate for the sake of automation</h3>

<p>The biggest opportunity NuGet facilitates unintended, is the fact you can get rid of those referenced binaries polluting your source control system, as <a href="/post/2011/07/18/Continuous-Package-Integration-NuGet-vs-Source-Control.html" target="_blank">I explained in an earlier post</a>. I'm really a big fan of this approach when set up properly. The NuGetPowerTools package (adding <em>functionality!</em>) is a must-have if you want to make life easy. After installing it, you won't have any binary dependency to any referenced library or what-so-ever. What you will get is a few extra commands ready to be used in the NuGet Package Manager Console. <a href="http://blog.davidebbo.com/2011/08/easy-way-to-set-up-nuget-to-restore.html" target="_blank">David Ebbo explains its usage</a> on his blog, but the key take-away relevant to my point here is that this tiny <em>functionality-adding package</em> is affecting the build process. Unlike the scaffolding packages, adding functionality to the IDE, this one is adding functionality to the <em>build process</em>: using MSBuild targets and the NuGet commandline, a pre-build step is added to the projects, fetching the NuGet packages from a configurable package source (or multiple sources).</p>

<p>Knowing all this, it's really important here that you keep in mind that some core principles didn't change... at all! Continuous integration is a proven important development practice and build automation plays a key part in it (<a href="http://paulstack.co.uk/blog/post/How-to-get-started-with-CI.aspx" target="_blank">Paul Stack's excellent series on the topic</a> should get you started). Here's where the other pitfall comes into play. Since we've changed the build process to <em>continuously integrate NuGet packages</em>, people start looking even for more opportunities. It is using the NuGet commandline behind the scenes, right? And it has an <em><a href="http://docs.nuget.org/docs/reference/command-line-reference#Update_Command" target="_blank">update</a></em> command, right? So... why don't we automatically update our packages to the latest version in our automated builds? I mean, it's safe, right? The command comes with a -Safe switch, so it must be!</p>

<p>Well, I'm not questioning whether it is safe or not (although you fully rely on package producers using proper versioning). I'm questioning whether you should even ask the question! </p>

<p>Each build of a given single version (changeset/revision) of your sources should produce the same result, always! If this is not the case, your builds are not reliable, because the state of your product has not changed. Same input produces same output.</p>

<p>If you allow your automated builds to change the version of the referenced packages you rely on (and these are <em>dependencies</em>, as they are <em>referenced</em> by your resulting binaries), you're effectively changing the <em>input</em> of the build process, resulting in a different output, without affecting the state of the product in your version control system. You are adding <em>state</em> to your build environment, and you loose it on every build! Your build of yesterday morning might be a different one than the one produced today, even if the entire team did not commit any changes. Maybe a new version of a package was released during the night, and got picked up by today's build?</p>

<p><strong>Conclusion #2: Do not auto-update NuGet packages during automated builds</strong> for the reasons explained above.</p>

<p>It's a small, but subtle difference between a NuGet <em>dependency</em> package, and a NuGet <em>functionality</em> package, but the implications can reach us from 'dependency hell' (what NuGet helps us to solve) to "bug management hell" (which may occur when bugs get reported on unreliable builds).</p>

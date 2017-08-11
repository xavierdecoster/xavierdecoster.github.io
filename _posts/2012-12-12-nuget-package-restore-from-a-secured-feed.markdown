---
layout: single
title: "NuGet Package Restore from a secured feed"
date: 2012-12-12 10:05:03 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/12/12/nuget-package-restore-from-a-secured-feed/"]
author: Xavier Decoster
redirect_from:
 - /2012/12/12/nuget-package-restore-from-a-secured-feed/.html
 - /2012/12/12/nuget-package-restore-from-a-secured-feed/.html
---
<p>One of the most frequently asked questions at <a href="http://www.myget.org">MyGet</a> is the following one (we have pending updates to our <a href="http://www.myget.org/site/Faq">FAQ</a> section):</p>

<p><em>How do I set up NuGet package restore against a private MyGet feed requiring authentication?</em></p>

<p>This is also one of the things you might end up doing when <a href="/debugging-nuget-package-restore">debugging NuGet package restore issues</a>.</p>

<p>For public feeds, you only need to change the repository URL in the nuget.targets file to let your build server know from where it needs to fetch the packages.
For private feeds however, there are a few things you need to know.</p>

<h3>Which credentials should I use?</h3>

<p>At MyGet, we recommend you to create a separate account for your build agents and give it specific permission on your feed (e.g. readonly or read/write, but no additional permissions). </p>

<p>It is not a technical requirement though: you could simply use your personal account, but please be aware that in this case you share your credentials! </p>

<p>As you'll see in this post, you can store the credentials for the build service account on the build agent(s) without having to share them with anyone. Using a user's account for the build agent can break anyone's build if access for this user is revoked...</p>

<h3>Visual Studio will prompt for credentials</h3>

<p>As soon as you try to communicate with a secured package source in Visual Studio, it will prompt you for credentials.</p>

<p><img src="http://xavierdecosterblog.blob.core.windows.net/blog/2012-12-12/2012-12-12_1014.png" alt="alt text" /></p>

<p>So why do you get the following build error when using package restore?</p>

<p><img src="http://xavierdecosterblog.blob.core.windows.net/blog/2012-12-12/2012-12-12_1513.png" alt="Cannot prompt for input in non-interactive mode" /></p>

<h3>There's no non-interactive way to provide credential parameters</h3>

<p>NuGet package restore relies on the <a href="https://nuget.org/nuget.exe">NuGet.exe</a> commandline tool by using the <a href="http://docs.nuget.org/docs/reference/command-line-reference#Install_Command">install</a> command. The commandline will either prompt you for credentials (which isn't suitable for automated build scenarios), or will look for credentials in nuget.config file in <code>%AppData%\NuGet\nuget.config</code> (if you use the Non-Interactive option). </p>

<p>The latter looks like what you need in automated build scenarios, but requires you to store feed credentials on the machine, for the user account that will perform the build. This can become cumbersome if you have a multitude of solutions using this feature.</p>

<h3>Hierarchical NuGet.config doesn't take credentials into account (yet!)</h3>

<p style="font-weight:bold;">Edit: As of v2.2 released on Dec 12, it is possible to use hierarchical config files for this. <a href="/nuget-package-restore-using-solution-specific">Check my follow-up post</a> on this matter.</p>

<p>The latest version of NuGet has support for hierarchical nuget.config files, which is an attempt to overcome the need to store everything on the machine. It allows you to have a solution-level NuGet configuration which should be taken into account during package restore. </p>

<p>This means that feed URL and credentials could be stored next to your solution instead of being pre-configured in the user profile. However, credentials aren't picked up (yet), and there's no easy way to store them (encrypted) into any nuget.config file other than the one in your roaming user profile (explained in the next section of this post).</p>

<p>This is a known issue which seems to be fixed in vNext of NuGet. Check <a href="http://nuget.codeplex.com/workitem/2718">this Codeplex issue</a> for more details. Not sure though whether this will be facilitated without having to copy-paste those encrypted credentials from one config to another.</p>

<h3>You can store feed credentials in your user-profile NuGet.config</h3>

<p>That's likely to be the easiest approach: as you register the package source URL, you might want to save the required credentials as well. This is however not exposed in the Visual Studio NuGet Package Manager extension, so you'll have to use the NuGet.exe commandline tool, which has a handy but fairly unknown <a href="http://docs.nuget.org/docs/reference/command-line-reference">config command</a> (anyone using a corporate proxy?) and <a href="http://docs.nuget.org/docs/reference/command-line-reference#Sources_Command">sources</a> command. The following gist illustrates a few of these options that should help you configure your secured feed, including credentials.</p>

<script src="https://gist.github.com/3205826.js"></script>

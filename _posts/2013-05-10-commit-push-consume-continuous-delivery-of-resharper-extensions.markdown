---
layout: single
title: "Commit, Push, Consume: continuous delivery of ReSharper extensions"
date: 2013-05-10 13:07:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["ReSharper","Visual Studio","NuGet","Package Management"]
alias: ["/2013/05/10/commit-push-consume-continuous-delivery-of-resharper-extensions/"]
author: Xavier Decoster
redirect_from:
 - /2013/05/10/commit-push-consume-continuous-delivery-of-resharper-extensions/.html
 - /commit-push-consume-continuous-delivery-of-resharper-extensions
---
<p><p>I've recently <a href="/a-resharper-plugin-to-detect-suspicious-semicolons-in-razor-views">announced my ReSharper Razor plugin</a> and published the EAP package to the brand new ReSharper Plugin feed. I've originally built it against the ReSharper 7 SDK and only recently added support for the <a href="http://confluence.jetbrains.com/display/ReSharper/ReSharper+8+EAP">ReSharper 8 EAP SDK</a>. One of the questions I asked myself when I built the R# 7 version was: how do I ship it? I was quite thrilled to receive an email from Matt Ellis (<a href="https://twitter.com/citizenmatt">@citizenmatt</a>) inviting me to take a look at the latest SDK bits which included the new NuGet-based Extension Manager! In this post I want to show you how I'm using free tools to develop, build, package, test &amp; publish a ReSharper plugin.
</p><h1>The missing link?
</h1><p>The plugin obviously is open source. The source repository for this plugin can be found on GitHub: <a href="https://github.com/xavierdecoster/ReSharper.RazorExtensions">https://github.com/xavierdecoster/ReSharper.RazorExtensions</a>.
</p><p>One of the first things I do when starting a new project is setting up Continuous Integration. Yes! Even for a little OSS pet project! In this case, the output of my build should be a NuGet package containing the plugin, so I need a repository to serve these CI packages for consumption to facilitate testing. Each GitHub repository has this great feature called <a href="https://help.github.com/articles/post-receive-hooks"><em>Post-Receive Hooks</em></a> which allows you to trigger another service whenever someone commits to the repository. Sounds useful, right?
</p><p>Let's rephrase that: whenever GitHub triggers a service, it should <em>build</em> the sources, <em>package</em> the plugin and <em>publish</em> it on a NuGet feed for consumption. I know another free service for that: <a href="http://www.myget.org/">MyGet</a>!
</p><p>MyGet "Wonka" <a href="http://blog.myget.org/post/2013/03/22/Whats-new-in-Build-Services.aspx">Build Services</a> allows you to connect your GitHub repository to a MyGet feed. It is designed to take care of building, testing, packaging and publishing <em>consumables</em>. I like to think in terms of <em>consumables</em> and <em>consumers</em>: don't try to build and package your enterprise web sites on there. We're likely missing some fancy SDKs anyway and we refuse to install Visual Studio <span style="font-family:Wingdings">J</span> For this scenario however it's perfect! But I'm biased <span style="font-family:Wingdings">J</span>
    </p><h1>Connecting the services
</h1><p>To develop a ReSharper plugin you need to reference the ReSharper SDK. It's not available on NuGet, so you'll need to download the MSI. It comes with some nice project and item templates for Visual Studio, and the SDK is quite large. My plugin only used a limited set of binaries from this SDK so I decided to commit them into Git (yeah, I know, bite me…). It's for the greater good, as the SDK won't be installed on the build agent and isn't available as a NuGet package to restore.
</p><p>When you're a MyGet feed owner, you can add and configure a <em>build source</em> for your feed. Click on the <em>Add from GitHub</em> button and select the repository you want.
</p><p><img src="/get/043013_2028_CommitPushC1_635029505354155325.png" alt="" style="max-width:600px;"/>
    </p><p>You'll need to authorize MyGet to get access to your GitHub repository, and you'll be prompted with a dialog to select the repository you want to connect to.
</p><p><img src="/get/043013_2028_CommitPushC2_635029505359311245.png" alt="" style="max-width:600px;"/>
    </p><p>We'll select the master branch by default, but you can obviously change that if you want. You don't need to edit anything else on this build source for now as you're already presented with a HTTP POST hook URL.
</p><p><img src="/get/043013_2028_CommitPushC3_635029505361654845.png" alt="" style="max-width:600px;"/>
    </p><p>Next up, connect your build source to GitHub. Browse your GitHub repository settings and look for the Service Hooks tab. There you're able to enter a <em>WebHook URL</em> as shown below. This URL will be hit with a POST request whenever someone pushes to your GitHub repository and passes some information about the push event.
</p><p><img src="/get/043013_2028_CommitPushC4_635029505365248365.png" alt="" style="max-width:600px;"/>
    </p><p>This provides enough information to MyGet in order to clone this repository and run a build. MyGet Build Services uses a conventional approach and selects one of the following artifacts in order of precedence:
</p><ol><li>Build.bat (or build.cmd or build.ps1)
</li><li>MyGet.sln
</li><li>Any other .sln
</li><li>All .csproj files (or other project types)
</li><li>All .nuspec files
</li></ol><p>It is up to you to ensure your repository is in a suitable format for an automated build. If you like full control over the build process, then you'll be happy to find a <a href="https://github.com/xavierdecoster/ReSharper.RazorExtensions/blob/master/build.bat">build.bat file in my repository</a>. That's it, whenever I push a commit to my GitHub repository, a MyGet build will be triggered: it will compile my sources, run any tests available, create a NuGet package for the plugin in the <a href="http://confluence.jetbrains.com/display/ReSharper/1.9+Packaging+%28R8%29">ReSharper plugin package format</a>, and push the package on my MyGet feed for consumption.
</p><h1>Consuming the plugin from a MyGet feed
</h1><p>Open the ReSharper Extension Manager (of all places, it's available in ReSharper &gt; Extension Manager), and click on <em>Settings</em>. Just as with the NuGet Visual Studio extension, the ReSharper Extension Manager allows you to configure any NuGet feed for consumption. Simply add your MyGet feed url into the list:
</p><p><img src="/get/043013_2028_CommitPushC5_635029505369623085.png" alt="" style="max-width:600px;"/>
    </p><p>Save your settings and reopen the Extension Manager: you'll find your newly added feed in there. All CI packages you publish on that feed will be available for consumption.
</p><p><img src="/get/043013_2028_CommitPushC6_635029505374466525.png" alt="" style="max-width:600px;"/>
    </p><p>Even better: ReSharper will notify you about available updates for your plugins!
</p><p><img src="/get/043013_2028_CommitPushC7_635029505377278845.png" alt="" style="max-width:600px;"/>
    </p><h1>Release some!
</h1><p>As soon as you're satisfied with your plugin, you can release it to the masses. Here I'm going to use MyGet's <em>package sources</em> feature: any MyGet feed can have one or more upstream NuGet feeds. This allows for various scenarios such as aggregating feeds, filtering, proxying &amp; mirroring, but also <em>package promotion</em>.
</p><p>In order release a CI package, you need to promote a package to the ReSharper Plugin feed (and optionally change the package version, e.g. strip the pre-release tag). To be able to do this, you first have to configure the ReSharper plugin feed as an upstream package source. We just <a href="http://blog.myget.org/post/2013/04/29/Create-a-list-of-favorite-ReSharper-plugins.aspx">announced the availability of a new <em>preset</em></a> for this on the MyGet blog.
</p><p>Browse to your feed settings and select the Package Sources tab. Select <em>Add Package Source</em> and click on the <em>Preset</em> button in the top-left of the dialog. You'll find a new <em>ReSharper extension gallery</em> preset. Don't forget to configure your API key for the <a href="http://resharper-plugins.jetbrains.com/">ReSharper Gallery</a> or your package can't be pushed upstream as the request would be unauthorized.
</p><p><img src="/get/043013_2028_CommitPushC8_635029505380559885.png" alt="" style="max-width:600px;"/>
    </p><p>Whenever you want to release a new version for consumption, simply promote your package to the release repository with a click of a button.<br/>
<img src="/images/2013-05-10_1504.png" style="max-width:600px;" alt="" />
</p><h1>Conclusion
</h1><p>Matt and the ReSharper team have really done a great job by adopting NuGet as a protocol for distributing plugins. It makes these plugins more discoverable and it's a breeze to install them or keep them up to date. As a plugin author (actually, any OSS developer), you should really leverage all the (often free for OSS!) tooling available: many organizations can only dream of such a streamlined development workflow. Releasing a new version of my plugin is really just a commit (or a pull request) away! Commit, push &amp; consume!</p></p>

---
layout: single
title: "Deploying to Azure Web Sites using NuGet package restore from a secured feed"
date: 2013-03-24 00:00:00 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2013/03/24/deploying-to-azure-web-sites-using-nuget-package-restore-from-a-secured-feed/"]
author: Xavier Decoster
redirect_from:
 - /2013/03/24/deploying-to-azure-web-sites-using-nuget-package-restore-from-a-secured-feed/.html
 - /2013/03/24/deploying-to-azure-web-sites-using-nuget-package-restore-from-a-secured-feed/.html
---
<p>
<b>
Update: This post pre-dates NuGet v2.7 which changed the NuGet package restore flow (it is now by default no longer part of the MSBuild process, but runs before the MSBuild process starts).
</b><br/>
The method explained below might no longer work and you'll need to upgrade your projects to the new NuGet package restore mechanism.<br/>
To do so:
<ol>
<li>Strip out the NuGet.targets file from your project(s)</li>
<li>Remove the .nuget folder (you can keep it but it's no longer needed)</li>
<li>Add this NuGet.config file into the path where your solution file resides:<br/>
<pre>
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;configuration&gt;
  &lt;packageSources&gt;
    &lt;add key="Feed name" value="https://www.myget.org/F/feed_goes_here/api/v2/" /&gt;
  &lt;/packageSources&gt;
  &lt;activePackageSource&gt;
    &lt;add key="All" value="(Aggregate source)" />
  &lt;/activePackageSource&gt;
&lt;/configuration&gt;
</pre></li>
</ol>
</p>

<hr/>

<p><b>Original post:</b></p>

<p><p><a href="http://www.windowsazure.com/en-us/home/scenarios/web-sites/">Windows Azure Web Sites</a> has this great feature to automate deployments from various source code repositories, such as Team Foundation Service, Codeplex, GitHub, DropBox, BitBucket or a local Git repository. A popular support request we receive at <a href="http://www.myget.org">MyGet</a> is the following one: <strong>"I have this web site in my hosted source code repository. How can I auto-deploy my web site and have it restore my NuGet packages from my private MyGet feed?" </strong>This is quite a common scenario. This post will focus on the set up of having a private GitHub repository and a private MyGet feed.
</p><h1>Creating &amp; configuring the Azure Web Site for auto-deployment from GitHub
</h1><p>I have a private GitHub repository containing the <a href="http://blog.ntotten.com/2012/07/24/windows-azure-web-sites-modern-application-sample-cloud-survey/">CloudSurvey sample web application from the Azure Toolkit</a>. It requires a Sql database so I'll set up Windows Azure Sql Database in one go as well. This is a breeze really: walk through the following screenies: next – next – next – finish.
</p><p><img src="/get/032413_1935_Deployingto1_634997505156711815.png" alt=""/>
    </p><p><img src="/get/032413_1935_Deployingto2_634997505160930295.png" alt=""/>
    </p><p><img src="/get/032413_1935_Deployingto3_634997505164992535.png" alt=""/>
    </p><p><img src="/get/032413_1935_Deployingto4_634997505169367235.png" alt=""/>
    </p><h1>Understanding the Deployment Workflow
</h1><p>When you connect your Windows Azure Web Site to GitHub, a post-commit hook will be created and connect the GitHub repository with a newly created remote on Windows Azure. As soon as someone pushes changes to the GitHub repository, this post-commit hook will be called and inform the <a href="https://github.com/projectkudu/kudu">Kudu</a> service about this event. Kudu will in turn do its magic in order to build and deploy the web site.
</p><p><img src="/get/032413_1935_Deployingto5_634997505173898166.png" alt=""/>
    </p><h1>NuGet Package Restore
</h1><p>Kudu takes care of the build and deployment fase. It fetches the changes linked to the commit that triggered the service, and prepares a local working environment. If your project is <a href="http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages">configured to use NuGet package restore</a>, then this will happen in a <em>pre-build step</em>. It is key to understand that NuGet package restore by default is <em>executed as part of the MSBuild process</em>. At least, that's how it works today.
</p><p>When you enable NuGet package restore on your solution, it downloads NuGet.targets (MSBuild), NuGet.exe and a NuGet.config. It also registers the NuGet.targets and some default settings into the project files that consume NuGet packages. Taking a closer look at the NuGet.targets file will reveal that a pre-build step is configured to call <em>nuget.exe install</em> on the packages.config file for the project being build. This install command is actually the restore command for the NuGet packages consumed by the project. It is <strong>configurable</strong> and <a href="http://docs.nuget.org/docs/reference/command-line-reference">documented</a>. In fact: so is the <a href="http://docs.nuget.org/docs/reference/nuget-config-file">NuGet.config</a> file!
</p><p>For the purpose of this post, I'll disable the default NuGet.org endpoint and redirect package restore to my private MyGet feed. The private MyGet feed requires basic authentication, so I'll need my MyGet credentials (you can set them in your MyGet user profile). The API key is not needed as that one's only required when pushing to the feed.
</p><p>Creating the private MyGet feed is just another walk in the park.
</p><p><img src="/get/032413_1935_Deployingto6_634997505178897814.png" alt=""/>
    </p><p>Next, you'll need to adjust the NuGet configuration to point to this package source. Open the $(SolutionDir).nuget\nuget.targets file and make sure you have disabled the check for package restore consent:
</p><p><pre>&lt;!-- Determines if package restore consent is required to restore packages --&gt;<br/>&lt;RequireRestoreConsent Condition=" '$(RequireRestoreConsent)' != 'false' "&gt;false&lt;/RequireRestoreConsent&gt;</pre></p><p>You also need to configure the MyGet private feed to be used as the one and only package source. Open your local nuget.config file and edit it to something as shown below. Note that I'm using the new <strong>ClearTextPassword</strong> capabilities in the <a href="http://build.nuget.org/NuGet.exe">latest dev-build</a> (<a href="http://nuget.codeplex.com/SourceControl/network/forks/XavierDecoster/NuGet2991/contribution/4018#!/tab/comments" target="_blank">yeay, pull request accepted!</a>):
</p><p><script src="https://gist.github.com/xavierdecoster/0b8b6fa4986b18283373.js"></script></p><p>Because by default NuGet encrypts feed credentials using machine specific information, these encrypted credentials are not portable. There's no way to encrypt them on-the-fly on WAWS:
</p><ul><li>the Kudu process does not have write permissions on the global nuget.config
</li><li>if you use the new –ConfigFile option to store the encrypted credentials in your local nuget.config, it will fail as well because the Kudu process runs with impersonation enabled or without loading the user profile
</li></ul><p>I've got <a href="http://nuget.codeplex.com/SourceControl/network/forks/XavierDecoster/ClearTextOptionInSourcesCommand/contribution/4311" target="_blank">another accepted pull-request</a> to add the –StorePasswordInClearText option to the nuget.exe sources command, so you should be able to run the following in the future as well:
</p><p><pre>nuget.exe sources add –Name &lt;feedname&gt; -User &lt;username&gt; -Password &lt;password&gt; -ConfigFile nuget.config -StorePasswordInClearText</pre>
</p><p>One last thing: adjust the RestoreCommand in your nuget.targets file to the following:
</p><p><pre>&lt;RestoreCommand&gt;$(NuGetCommand) install "$(PackagesConfig)" -NonInteractive $(RequireConsentSwitch) -solutionDir "$(SolutionDir) "&lt;/RestoreCommand&gt;</pre></p><p>And that's it: you should be up and running now. Your build log should contain the following trace:
</p><p><pre>1>  Using credentials from config. UserName: xavierdecoster</pre></p><p>One last tip in case you hit any issues or you need more information to debug: all nuget.exe commands support a –verbosity switch, so simply add <em>–verbosity detailed</em> at the end of the commands in the nuget.targets file to get more verbose output. Can't wait for the v2.5 release :-)</p></p>

<h1>How's that secure?!</h1>

<p>Exactly... For now: clear text is the only supported credential format. I'd love to see a new section in the WAWS Management portal though: nuget.config overrides. Similar to how you can secure your storage credentials or other web.config settings. Assuming the default convention of $(SolutionDir)\.nuget\nuget.config, it cannot be that hard to build?</p>

<p>As for the credentials to use: you should use an account with limited permissions for package restore. At MyGet you can easily create an account with only read-access on your NuGet repository. Please do that and use that one instead of your admin account! Also, if you don't want people to get access to your private feed, simply use a private GitHub or BitBucket repository.</p>

<p>Anyway, many thanks to the NuGet team for the new -ConfigFile command option, and for hinting into sending another PR to add the -StorePasswordInClearText option as well!</p>

---
layout: single
title: "Setting up your own SymbolSource Server: step-by-step"
date: 2012-08-07 22:16:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["Symbols"]
alias: ["/2012/08/07/setting-up-your-own-symbolsource-server-step-by-step/"]
author: Xavier Decoster
redirect_from:
 - /2012/08/07/setting-up-your-own-symbolsource-server-step-by-step/.html
 - /setting-up-your-own-symbolsource-server-step-by-step
---
<p style="font-weight:bold; font-size:1.5em; border:1px solid #eee; background-color:#fff; padding: 10px; line-height:1.5em;">Edit: This post is rather out-dated, and I would now recommend you <span style="font-style:italic; background-color:#FFFBCC">get yourself out of the symbols server hosting business</span>, and instead, use MyGet's Symbol Server capabilities.<br/>
More info: <a href="http://docs.myget.org/docs/reference/symbols">http://docs.myget.org/docs/reference/symbols</a>.</p>

<p>I've already explained <a href="/post/2011/11/16/Why-everyone-should-be-using-a-symbol-server.html">why everyone should be using a symbols server</a>. In that post, I explained that TFS comes with its own integrated symbols server, but <span style="text-decoration: underline;">at the moment</span> it doesn't play very nice with NuGet packages. Or maybe you are not using TFS at all.</p>

<p>That's where <a href="http://www.symbolsource.org/">SymbolSource</a> comes in. NuGet has the ability to <a href="http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-symbol-package">create symbols packages</a>, so you can easily push them to a symbols package repository. This is exactly what SymbolSource offers, either as a public or private repository on SymbolSource.org, or as a separate cloud-based instance of SymbolSource where you also can manage users and repositories. Both <a href="http://www.symbolsource.org/Public/Account/Register">plans</a> are still in an unlimited free beta mode, so <a href="http://www.symbolsource.org/Public/Account/Register">give it a try</a>.</p>

<p>As much as I am a huge fan of the hosted SymbolSource offering, some people prefer to host code, symbols and packages on their own infrastructure, if only to troll some sys-admins or slow down development (if you're a manager, I'm talking about <em>security</em>).</p>

<p>With the release of the SymbolSource <a href="http://www.symbolsource.org/Public/Blog/View/2012-03-13/Releasing_the_community_edition_of_SymbolSource">community edition</a>, I figured it was about time to try it out for myself.</p>

<h2>Prerequisites</h2>

<p>Before you open Visual Studio and search NuGet for the 'symbolsource' keyword, you'll have to install the <a href="http://msdn.microsoft.com/en-us/windows/hardware/gg463009">Debugging Tools for Windows</a>. As I'm going to try it out on a Windows Azure Virtual Machine running Windows Server 2012 RC (living on the edge aren't we), I'll simply install the Windows SDK for Windows 8 Release Preview which contains the <strong>Debugging Tools for Windows</strong> as a standalone component.</p>

<p><img alt="" src="/images/2012-08-07/install_debugging_tools.png" width="600"></p>

<p><strong>Keep track of the installation path</strong> as you will need it later on.</p>

<h2>Setting up your basic SymbolSource server</h2>

<p>If you are familiar with setting up your own NuGet server (if not, check out <a href="http://bit.ly/ProNuGet">this book</a>), you'll notice that setting up your own SymbolSource server is peanuts. Very similar to the NuGet.Server package, SymbolSource provides you with a <a href="http://nuget.org/packages/SymbolSource.Server.Basic">SymbolSource.Server.Basic</a> package on NuGet (they just released v1.1.0 so go get it while it's hot!). Let's get this thing rolling...</p>

<h3>Creating the Web application</h3>

<p>I started off an <em>empty</em> ASP.NET MVC4 application.</p>

<p><img src="/images/2012-08-07/new_mvc_project.png" width="600px"></p>

<p>Next I ran the following script in the NuGet Package Manager Console:</p>

<pre class="brush: plain; gutter: true; first-line: 1; tab-size: 2;  toolbar: false;">Install-Package SymbolSource.Server.Basic</pre>

<p>For those who prefer clicking there way through the UI, you can achieve the same by right clicking your Web project's references, select Manage NuGet Packages, and query the NuGet official feed for the term "SymbolSource.Server".</p>

<p><img alt="" src="/images/2012-08-07/install_symbsrc_basic_server_pkg_ui.png" width="600"></p>

<p>Installing this package injects a ton of dependencies into your consuming Web application project. This isn't a bad thing, really, because you get a whole lot of functionality by simply installing this single NuGet package. It even comes with <a href="http://nuget.org/packages/elmah" target="_blank">ELMAH</a> logging preconfigured, so you get logging out of the box.</p>

<p><strong>Note:</strong> Don't forget about security! See <a href="http://code.google.com/p/elmah/wiki/SecuringErrorLogPages">http://code.google.com/p/elmah/wiki/SecuringErrorLogPages</a> for more information on remote access and securing ELMAH.</p>

<p>Now's the time you need to configure the installation path of <em>Debugging Tools for Windows</em>. To do so, open the Web.config file and adjust the <appsettings> section. You'll find a predefined SrcSrvPath element with the default installation path for a x64 machine running Windows 8 or Windows Server 2012. If you chose a different installation path, or if you are running a previous version of Windows, simply replace the value with your installation path.</appsettings></p>

<p>Windows Server 2012 / Windows 8:</p>

<pre class="brush: xml; gutter: true; first-line:1; tab-size:2; toolbar: false;">&lt;add key="SrcSrvPath" value="C:\Program Files (x86)\Windows Kits\8.0\Debuggers\x64\srcsrv" /&gt;</pre>

<p>Earlier versions of Windows:</p>

<pre class="brush: xml; gutter: true; first-line:1; tab-size:2; toolbar: false;">&lt;add key="SrcSrvPath" value="C:\Program Files\Debugging Tools for Windows (x86)\srcsrv" /&gt;</pre>

<p>You can now start the application and browse the home page shown below.</p>

<p><img alt="" src="/images/2012-08-07/symsrv_failing_push_test.png" width="600"></p>

<p>All the information you need, including all required URLs, is listed on the application's start page.</p>

<ul>
<li><strong>Visual Studio Debugging URL</strong> - You need to configure this URL in the Debug settings of your Visual Studio development environment. More info and a recommended setup are <a href="http://www.symbolsource.org/Public/Home/VisualStudio" target="_blank">documented on the SymbolSource Web site</a>.</li>
<li><strong>NuGet Symbols Packages Repository URL</strong> - This is the NuGet package repository where you should push your symbols packages to.</li>
<li><strong>OpenWrap Repository URL</strong> - Yep, SymbolSource supports both NuGet and OpenWrap!</li>
<li><strong>Self-diagnostics</strong> - Very useful and very nice from the SymbolSource guys to provide this. This will help you verify whether the prerequisite is installed and your Web site is configured correctly to provide a fully functioning SymbolSource basic server. Shield this info from the public! No one needs to know...</li>
</ul>

<p style="padding-top: 1em;">
  If you look at the previous image, you'll notice that the NuGet push test failed with an InternalServerError. Checking this on the server itself reveals that the application has no access to the App_Data folder. To fix this, simply add write permissions for the IUSR on the <b>App_Data</b> folder through IIS Management Console. Make sure you do the same for the <b>Data</b> and the <b>Index</b> folder.
</p>

<p><img src="/images/2012-08-07/edit_site_permissions.png" alt=""></p>

<p><img src="/images/2012-08-07/edit_security_folder.png" alt=""></p>

<p><img src="/images/2012-08-07/add_IUSR_WritePermissions.png" alt=""></p>

<p>There's one last thing you need to do: modify your web.config and add the following elements:</p>

<pre class="brush:xml; gutter: true; first-line:1; tab-size:2; toolbar: false;">&lt;system.webServer&gt;<br />&nbsp;&lt;modules runAllManagedModulesForAllRequests="true" /&gt;<br />&lt;/system.webServer&gt;</pre>

<p>If all went well, your home page should indicate that your server has been configured correctly.</p>

<p><img src="/images/2012-08-07/symsrv_homepage.png" alt=""></p>

<h2>Creating and pushing symbols packages</h2>

<p>A symbols package has the *.symbols.nupkg extension and contains only DLL, PDB, XMLDOC and source files. To create them, add the -Symbols option to the NuGet pack command. You should see two packages being created instead of one. A detailed description on the symbols package contents and structure can be found on the online <a href="http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-symbol-package" target="_blank">NuGet Documentation.</a></p>

<p>Quoting from the SymbolSource documentation:</p>

<p><em>"At this moment, the server accepts any NuGet API key, but you are free to secure it using any of the IIS authentication capabilities."</em></p>

<p>This means you can easily push the symbols package using the following command:</p>

<pre class="brush:plain; gutter: true; first-line:1; tab-size:2; toolbar: false;">nuget push {packageID}.symbols.nupkg {API key} -source {url}</pre>

<p>The server will ignore your API key (as it accepts anything at the moment). Replace the {url} placeholder with the NuGet symbols packages repository URL you found on your server's home page.</p>

<p>That's it! Happy packaging!</p>

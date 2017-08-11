---
layout: post
title: "NuGet Package Restore using solution-specific NuGet.config"
date: 2012-12-13 08:57:52 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/12/13/nuget-package-restore-using-solution-specific/"]
author: Xavier Decoster
redirect_from:
 - /2012/12/13/nuget-package-restore-using-solution-specific/.html
 - /2012/12/13/nuget-package-restore-using-solution-specific/.html
---
<p>In my post on <a href="/nuget-package-restore-from-a-secured-feed">how to restore packages from a secured feed</a>, I already anticipated an important fix in the upcoming 2.2 release of NuGet to further facilitate this using hierarchical config files. Well, today, that 2.2 version got released, so let's take a look at the hierarchical config approach in more detail.</p>

<p>The hierarchical configuration feature allows you to have a solution-level <code>NuGet.config</code> file which should be taken into account during package restore. For feed credentials, this wasn't the case until now.</p>

<p>When enabling NuGet package restore, you'll notice a <code>.nuget</code> folder is created in the solution directory. Inside that folder resides the solution-level NuGet.config. This config file can be used to deviate from the global NuGet.config file you'll find in your roaming user profile: <code>%appdata%\NuGet\NuGet.config</code>.</p>

<p>Now imagine you want to restore NuGet packages from a secured feed, for instance from <code>https://www.myget.org/F/mysecuredfeed/</code>, or any feed requiring basic authentication using a username and a password.</p>

<p>You'll need to point NuGet to this feed URL and provide the required credentials. There are only <a href="/nuget-package-restore-from-a-secured-feed">a few options</a>, but as of today, when using v2.2 of NuGet, you might want to prefer this one.</p>

<p>In the .nuget\NuGet.targets (MSBuild) file, ensure package restore is pointing to your secured feed. Look for this ItemGroup section and add it in there:</p>

<pre class="brush: xml;auto-links:false;toolbar:false;">
&lt;ItemGroup Condition=" '$(PackageSources)' == '' "&gt;
    &lt;!-- Package sources used to restore packages. By default, registered sources under %APPDATA%\NuGet\NuGet.Config will be used --&gt;
    &lt;!-- The official NuGet package source (https://nuget.org/api/v2/) will be excluded if package sources are specified and it does not appear in the list --&gt;
    &lt;!--
        &lt;PackageSource Include="https://nuget.org/api/v2/" /&gt;
    --&gt;
    &lt;PackageSource Include="http://www.myget.org/F/mysecuredfeed/" /&gt;
&lt;/ItemGroup&gt;
</pre>

<p>By default, the solution-level NuGet.config file looks like this:</p>

<pre class="brush: xml;auto-links:false;toolbar:false;">
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;configuration&gt;
  &lt;solution&gt;
    &lt;add key="disableSourceControlIntegration" value="true" /&gt;
  &lt;/solution&gt;
&lt;/configuration&gt;
</pre>

<p>We need to somehow add the feed credentials in there. I haven't found a <em>straightforward</em> way of doing so, other than the following one:</p>

<ol>
<li>Ensure the feed is registered in your global NuGet.config by running <br/><code>nuget sources add -name "My Secured Feed" -source https://www.myget.org/F/mysecuredfeed/</code>.</li>
<li>Next, register the feed credentials in the global NuGet.config by running <br/><code>nuget sources update -Name "My Secured Feed" -User &lt;username&gt; -Pass &lt;password&gt;</code>.</li>
<li>Open the global NuGet.config file (located in <code>%appdata%\NuGet\NuGet.config</code>) in a text editor, and copy the <code>packageSourceCredentials</code> section (only include the feed credential child-nodes you need for your solution). You'll notice the password is stored in an encrypted format.</li>
<li>Open your solution-level NuGet.config file (located in <code>$(SolutionDir)\.nuget\NuGet.config</code>), and paste the <code>packageSourceCredentials</code> section into the <code>configuration</code> root of the XML file.</li>
</ol>

<p>Your solution-level NuGet.config should now look similar to the following:</p>

<pre class="brush: xml;auto-links:false;toolbar:false;">
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;configuration&gt;
  &lt;solution&gt;
    &lt;add key="disableSourceControlIntegration" value="true" /&gt;
  &lt;/solution&gt;
  &lt;packageSourceCredentials&gt;
    &lt;My_x0020_Secured_x0020_Feed&gt;
      &lt;add key="Username" value="username" /&gt;
      &lt;add key="Password" value="AQAAANCMnd8BFdERjHoAwE/Cl+sBAAAAtA/nIrdkAEaYf19cCBs6wgAAAAACAAAAAAAQZgAAAAEAACAAAABCszDHAgQ2OZASfFIGGmQKUTa4SwEqM9erKl1WoHsZDAAAAAAOgAAAAAIAACAAAACMsh26fEmwHSPz3DTzcEGuk+V/CjlAZWb2s5t2Tcr22BAAAADrMOh0Yn8FaydTiyWKIWD9QAAAAB9o2+fEdSaztWNgzjU1eBnI/aOjR95kKaJYMqF2d3LaOri6QUZ40Zm9kqa0RC/DkTTSMAr5DOES2dt0OmdiNi0=" /&gt;
    &lt;/My_x0020_Secured_x0020_Feed&gt;
  &lt;/packageSourceCredentials&gt;
&lt;/configuration&gt;
</pre>

<p>Et voilà! </p>

<p>That's all folks, happy packaging!</p>

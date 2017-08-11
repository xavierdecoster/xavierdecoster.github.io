---
layout: single
title: "Install-Package NuSpec"
date: 2012-04-27 21:44:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/04/27/install-package-nuspec-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2012/04/27/install-package-nuspec-aspx/.html
 - /2012/04/27/install-package-nuspec-aspx/.html
---
<p>If you are like me and regularly produce NuGet packages, then you often deal with NuSpec files. I always found it annoying that I had to leave my Visual Studio environment in order to create a .nuspec file. Well, I've finally automated this step and published it on <a href="https://github.com/xavierdecoster/NuGetPackages/tree/master/NuSpec" target="_blank">Github</a> and on the <a href="https://nuget.org/packages/NuSpec" target="_blank">NuGet Gallery</a>. Feel free to fork it, contribute and provide feedback!</p>

<table style="vertical-align: top;" border="0">
  <tbody>
    <tr>
      <td style="border:none;">
          <img alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-27/2012-04-27_1359.png" />
      </td>

      <td style="vertical-align: top; border:none;">
        <h1>
          What this package does
        </h1>

        <p>
          Plain simple:
        </p>

        <ul>
          <li>
            It extends the NuGet Package Manager Console with a new PowerShell cmdlet, called Install-NuSpec
          </li>
          <li>
            It adds the nuspec XSD's to the Solution Items solution-folder, providing you with IntelliSense when editing the nuspec file.
          </li>
          <li>
            It adds a tokenized nuspec file to the target project and renames the nuspec file to <projectname>.nuspec (which allows the PackageBuild feature - or <em>nuget pack <project> </em>- to pick it up as well)
          </li>
        </ul>

        <div>
          Please note that this package obsoletes my old NuGet.Manifest.Xsd package as well.
        </div>
      </td>
    </tr>
  </tbody>
</table>

<p>Under the hood, I'm using an XML template for the generated .nuspec files, which looks like this:</p>

<pre class="brush: xml;auto-links:false;toolbar:false;">&lt;?xml version="1.0"?&gt;
&lt;!--
This tokenized .nuspec manifest file is targeting the latest NuGet namespace,
which ensures you can benefit from the latest packaging features.

If desired, you can change the XML-namespace and target the original XSD.
To do so, replace the current package declaration by the following:

&lt;package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd"&gt;

Run "nuget.exe pack &lt;project&gt;" to build a nuget package for this project
using this tokenized nuspec file.

--&gt;
&lt;package xmlns="http://schemas.microsoft.com/packaging/2011/08/nuspec.xsd"&gt;
  &lt;metadata&gt;
    &lt;version&gt;$version$&lt;/version&gt;
    &lt;authors&gt;$author$&lt;/authors&gt;
    &lt;owners /&gt;
    &lt;id&gt;$id$&lt;/id&gt;
    &lt;title /&gt;
    &lt;requireLicenseAcceptance&gt;false&lt;/requireLicenseAcceptance&gt;
    &lt;description&gt;$description$&lt;/description&gt;
  &lt;/metadata&gt;
  &lt;files /&gt;
&lt;/package&gt;</pre>

<p>Install the package use <em>Install-Package NuSpec</em>.</p>

<p><img style="max-width: 650px;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-27/2012-04-27_1356.png" /></p>

<p>And start using the Install-NuSpec command instead of losing time messing arround with files and windows and stuff. (kittens die when you do that)</p>

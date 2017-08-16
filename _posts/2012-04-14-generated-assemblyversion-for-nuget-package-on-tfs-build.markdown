---
layout: single
title: "Generated AssemblyVersion for NuGet package on TFS Build"
date: 2012-04-14 21:26:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["ALM","NuGet","TFS","Package Management"]
alias: ["/2012/04/14/generated-assemblyversion-for-nuget-package-on-tfs-build/"]
author: Xavier Decoster
redirect_from:
 - /2012/04/14/generated-assemblyversion-for-nuget-package-on-tfs-build/.html
 - /2012/04/14/generated-assemblyversion-for-nuget-package-on-tfs-build/.html
---
<h2>Goal: SemVer &amp; auto-incremented build number on package &amp; project output</h2>

<p>Imagine you want to auto-increment the build number in your <a href="http://msdn.microsoft.com/en-us/library/system.reflection.assemblyversionattribute(v=vs.100).aspx" target="_blank">AssemblyVersion</a> during Continuous Integration, and meanwhile keep control over the first three version numbers (major.minor.patch). This would allow you to apply <a href="http://semver.org" target="_blank">Semantic Versioning</a> whilst generating new builds produce new assemblies/packages with a higher version number.</p>

<p>The NuGet command line allows you to fairly easy <a href="http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-package#From_a_project" target="_blank">target a project file</a> or a nuspec file (NuGet package manifest). If you are familiar with targeting a project file, you know you can use <a href="http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-package#From_a_project" target="_blank">token placeholders</a> for some parts of the package metadata, e.g. $id$, $version$ etc.</p>

<p>A rather annoying aspect of TFS Build lays in the fact that your compiled project output ends up redirected into a folder called <em>Binaries</em> (and there only!). If your build definition workspace is set to the solution directory (and you didn't modify this part of the Build Definition Template), this Binaries folder can be found here: $(SolutionDir)..\Binaries. Yes, one level up: it is a sibling of your checkout folder (called <em>Sources</em>) on the build agent.</p>

<h2>There's no easy way out</h2>

<p>There are some options at your disposal to get you going for a few hours trying to complete this puzzle.</p>

<ul>
<li>The NuGet command line <em><a href="http://docs.nuget.org/docs/reference/command-line-reference#Pack_Command" target="_blank">pack</a></em> command has an extra option: <em>BasePath</em></li>
<li>You'll have to play with <em>relative paths</em> to include only the stuff you want into your NuGet package</li>
<li>Which also means you'll need to create a NuGet package manifest (<em><a href="http://docs.nuget.org/docs/reference/nuspec-reference" target="_blank">nuspec</a></em>) upfront</li>
</ul>

<p>I spent quite some time trying to figure out how to accomplish this with minimal changes. It actually turned out that there seems to be <a href="http://nuget.codeplex.com/workitem/2103" target="_blank">a bug in the NuGet command line</a>, causing the BasePath to be ignored in certain cases (we are in case #3 if you follow that link).</p>

<h2>This works for me</h2>

<h3>Prerequisites</h3>

<ul>
<li><a href="http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages" target="_blank">Enable NuGet PackageRestore</a>. This will give you the nuget.exe and some required MSBuild targets &amp; settings in the $(SolutionDir).nuget folder.</li>
<li><p>Create a NuGet package manifest for your target project(s) (<a href="http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-package#From_a_project" target="_blank">here's how</a>) and use relative paths to include those files you need to be packaged from the Binaries folder:Â  <pre class="brush: xml;gutter:false;">&lt;files&gt;
&lt;file src="....\Binaries\mylibrary.dll" target="lib\net40"/&gt;
&lt;/files&gt;
</pre></p></li>
<li><p>Get the latest edition of the <a href="http://msbuildextensionpack.codeplex.com/" target="_blank">MSBuild Extension Pack</a> from Codeplex (I used April 2012 edition)</p></li>
<li>Unzip that extension pack and find the following files in the 4.0.5.0 Binaries folder:  <em>Ionic.Zip.dll</em>, <em>MsBuildExtensionPack.dll *and *MSBuild.ExtensionPack.VersionNumber.targets</em>. Add them to the $(SolutionDir).nuget\MsBuildExtensionPack folder and make sure you add them to source control.<br/><br/><img src="/images/2012-04-14/msbuildextensions.png" alt="" /></li>
</ul>

<h3>Modifications</h3>

<p>Modify the <em>nuget.targets</em> file you'll find in the $(SolutionDir).nuget folder to look like this:</p>

<pre class="brush: xml;auto-links:false;">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003"&gt;
  &lt;Import Project="MsBuildExtensionPack\MSBuild.ExtensionPack.VersionNumber.targets" Condition="$(BuildPackage) == 'true'"/&gt;
  &lt;Import Project="NuGet.settings.targets"/&gt;

  &lt;Target Name="SetPackageVersion" Condition="$(BuildPackage) == 'true'"&gt;
    &lt;PropertyGroup&gt;
      &lt;!-- Fetch the generated assembly version from the AssemblyInfo task (MSBuild Extension Pack, April 2012)--&gt;
      &lt;PackageVersion&gt;$(MaxAssemblyVersion)&lt;/PackageVersion&gt;

      &lt;!-- If no NuSpec file defined in the project, fallback on the project itself--&gt;
      &lt;NuSpecFile Condition="$(NuSpecFile) == ''"&gt;$(ProjectPath)&lt;/NuSpecFile&gt;

      &lt;!-- Override BuildCommand with generated package version, if any --&gt;
      &lt;BuildCommand Condition="$(PackageVersion) != ''"&gt;"$(NuGetExePath)" pack "$(NuSpecFile)" -p Configuration=$(Configuration) -o "$(PackageOutputDir)" -symbols -version $(PackageVersion)&lt;/BuildCommand&gt;
    &lt;/PropertyGroup&gt;

    &lt;!-- Log the generated package version if any --&gt;
    &lt;Message Text="Generated package version '$(PackageVersion)' from built assembly" Importance="high" /&gt;
  &lt;/Target&gt;

  &lt;Target Name="RestorePackages" DependsOnTargets="CheckPrerequisites"&gt;
    &lt;Exec Command="$(RestoreCommand)"
          LogStandardErrorAsError="true"
          Condition="Exists('$(PackagesConfig)')" /&gt;
  &lt;/Target&gt;

  &lt;Target Name="BuildPackage" DependsOnTargets="CheckPrerequisites; SetPackageVersion"&gt;
    &lt;Exec Command="$(BuildCommand)"
          LogStandardErrorAsError="true" /&gt;
  &lt;/Target&gt;
&lt;/Project&gt;</pre>

<p>Edit the project file for which you want to create a NuGet package after compilation, and add the following MSBuild property in the Release configuration (or the configuration you use to build on TFS):</p>

<pre class="brush: xml;gutter:false; hightlight: [3]">&lt;PropertyGroup Condition="'$(Configuration)|$(Platform)' == 'Release|Any CPU'"&gt;
   &lt;!-- Shortened for brevity --&gt;
   &lt;NuSpecFile&gt;relativePathFromProjectFileTo.nuspec&lt;/NuSpecFile&gt;
&lt;/PropertyGroup&gt;</pre>

<p>Change the project's output location in the Release configuration (or the configuration you use to build on TFS) to *....\Binaries* <img style="width: 600px;" alt="" src="/images/2012-04-14/2012-4.png" /></p>

<h2>Controlling the Semantic Version</h2>

<p>Note that <span style="text-decoration: underline;">you can't use any token placeholders</span> with this solution. Actually, you might not need them anyway, since we set the (generated) version directly using the <em>pack -version</em> command.</p>

<p>To manage the <em>semantic</em> part of the version number (major.minor.patch), you can simply tweak some properties in the <em>MsBuild.ExtensionPack.VersionNumbers.targets</em> file. Yes, this is done manually, because there's nothing out there that can determine a semantic version for you.</p>

<p>Close to the top of that file, you'll find the properties that you need to tweak in order to change the first 3 version numbers.</p>

<pre class="brush: xml;auto-links:false;">&lt;!-- Properties for controlling the Assembly Version --&gt;
  &lt;PropertyGroup&gt;
    &lt;AssemblyMajorVersion&gt;1&lt;/AssemblyMajorVersion&gt;
    &lt;AssemblyMinorVersion&gt;0&lt;/AssemblyMinorVersion&gt;
    &lt;AssemblyBuildNumber&gt;0&lt;/AssemblyBuildNumber&gt;
    &lt;AssemblyRevision&gt;&lt;/AssemblyRevision&gt;
    &lt;AssemblyBuildNumberType&gt;&lt;/AssemblyBuildNumberType&gt;
    &lt;AssemblyBuildNumberFormat&gt;00&lt;/AssemblyBuildNumberFormat&gt;
    &lt;AssemblyRevisionType&gt;AutoIncrement&lt;/AssemblyRevisionType&gt;
    &lt;AssemblyRevisionFormat&gt;00&lt;/AssemblyRevisionFormat&gt;
  &lt;/PropertyGroup&gt;
</pre>

<p>The build output looks like this:</p>

<p><img style="max-width: 600px;" alt="" src="/images/2012-04-14/buildoutput.png" /></p>

<h2>Bonus: auto-push CI packages to MyGet</h2>

<p>Or any other NuGet package repository for that matter. It is actually very easy to extend this setup in such way that the built package is automatically pushed to a CI feed for instance.</p>

<p>To minimize the modifications to the original nuget.targets and nuget.settings.targets files, I've put all my custom msbuild properties, overrides and targets into a separate nuget.Extensions.targets file, which I saved in the .nuget folder. As such, I only have to import the nuget.Extensions.targets file into the nuget.targets file, and make the BuildPackage target depend on the SetPackageVersion target. I've also added a conditional PushPackage call in the BuildPackage target.</p>

<p>Don't forget to enable auto-push by setting the new PushPackage MSBuild property to True in your project file, in the Release configuration (or the configuration you use in the build definition).</p>

<pre class="brush: xml; highlight: [4]">&lt;PropertyGroup Condition="'$(Configuration)|$(Platform)' == 'Release|Any CPU'"&gt;
    &lt;OutputPath&gt;..\..\Binaries\Release\&lt;/OutputPath&gt;
    &lt;BuildPackage&gt;true&lt;/BuildPackage&gt;
     &lt;PushPackage&gt;true&lt;/PushPackage&gt;
    &lt;NuSpecFile&gt;relativePathFromProjectTo.nuspec&lt;/NuSpecFile&gt;
&lt;/PropertyGroup&gt;</pre>

<h2>Bonus 2: auto-push CI Symbols packages to SymbolSource</h2>

<p>One of the interesting features MyGet offers for any private NuGet feed, is its integration with <a href="http://www.symbolsource.org/" target="_blank">SymbolSource</a>. Did you know that when you create a <em>private</em>Â feed on MyGet, you automatically get a private SymbolSource repository at your disposal, with the same shared API key? Maarten explains it <a href="http://blog.maartenballiauw.be/post/2011/11/16/Publishing-symbol-packages-for-a-MyGet-feed.aspx" target="_blank">here</a>.</p>

<p>I simply added an additional PushSymbolsCommand and reproduced the same steps as for the PushPackageCommand.Â You can find my complete nuget.targets and nuget.Extensions.targets file below.</p>

<h3>NuGet.targets</h3>

<pre class="brush: xml; highlight: [4,10,13,15];auto-links:false;">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003"&gt;
 &lt;Import Project="NuGet.settings.targets"/&gt;
 &lt;Import Project="NuGet.Extensions.targets" /&gt;

 &lt;Target Name="RestorePackages" DependsOnTargets="CheckPrerequisites"&gt;
  &lt;Exec Command="$(RestoreCommand)" LogStandardErrorAsError="true" Condition="Exists('$(PackagesConfig)')" /&gt;
 &lt;/Target&gt;

 &lt;Target Name="BuildPackage" DependsOnTargets="CheckPrerequisites; SetPackageVersion"&gt;
  &lt;Exec Command="$(BuildCommand)" LogStandardErrorAsError="true" /&gt;

  &lt;Exec Command="$(PushCommand)" LogStandardErrorAsError="true" Condition="Exists('$(NuPkgFile)') And $(PushPackage) == 'true'"/&gt;

  &lt;Exec Command="$(PushSymbolsCommand)" LogStandardErrorAsError="true" Condition="Exists('$(SymbolsPkgFile)') And $(PushPackage) == 'true'"/&gt;
 &lt;/Target&gt;
&lt;/Project&gt;</pre>

<h3>NuGet.Extensions.targets</h3>

<pre class="brush: xml;auto-links:false; gutter:false;">&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003"&gt;
 &lt;Import Project="MsBuildExtensionPack\MSBuild.ExtensionPack.VersionNumber.targets" Condition="$(BuildPackage) == 'true'"/&gt;
 &lt;Target Name="SetPackageVersion" Condition="$(BuildPackage) == 'true'"&gt;
  &lt;PropertyGroup&gt;
   &lt;!-- Fetch generated assembly version from AssemblyInfo task (MSBuild Extension Pack --&gt;
   &lt;PackageVersion&gt;$(MaxAssemblyVersion)&lt;/PackageVersion&gt;
   &lt;PushPkgSource&gt;http://www.myget.org/F/yourfeedname/&lt;/PushPkgSource&gt;
   &lt;SymbolsPkgSource&gt;http://nuget.gw.symbolsource.org/MyGet/yourfeedname&lt;/SymbolsPkgSource&gt;
   &lt;PushApiKey&gt;yourApiKey&lt;/PushApiKey&gt;

   &lt;!-- Property that enables pushing a package from a project --&gt;
   &lt;PushPackage Condition="$(PushPackage) == ''"&gt;false&lt;/PushPackage&gt;

   &lt;!-- Derive package file name in case the package will be pushed & nuspec is defined --&gt;
   &lt;NuPkgFile Condition="$(PushPackage) == 'true' And $(NuSpecFile) != ''"&gt;$(PackageOutputDir)\$(NuSpecFile.Trim('nuspec'))$(PackageVersion).nupkg&lt;/NuPkgFile&gt;
   &lt;SymbolsPkgFile Condition="$(PushPackage) == 'true' And $(NuPkgFile) != ''"&gt;$(NuPkgFile.Trim('nupkg')).Symbols.nupkg&lt;/SymbolsPkgFile&gt;

   &lt;!-- If no NuSpec file defined in the project, fallback on the project itself--&gt;
   &lt;NuSpecFile Condition="$(NuSpecFile) == ''"&gt;$(ProjectPath)&lt;/NuSpecFile&gt;

   &lt;!-- Override BuildCommand with generated package version, if any --&gt;
   &lt;BuildCommand Condition="$(PackageVersion) != ''"&gt;"$(NuGetExePath)" pack "$(NuSpecFile)" -p Configuration=$(Configuration) -o "$(PackageOutputDir)" -symbols -version $(PackageVersion)&lt;/BuildCommand&gt;

   &lt;!-- Added bonus: push command --&gt;
   &lt;PushCommand&gt;"$(NuGetExePath)" push "$(NuPkgFile)" -source "$(PushPkgSource)" -apikey $(PushApiKey)&lt;/PushCommand&gt;
   &lt;PushSymbolsCommand&gt;"$(NuGetExePath)" push "$(SymbolsPkgFile)" -source "$(SymbolsPkgSource)" -apikey $(PushApiKey)&lt;/PushSymbolsCommand&gt;
  &lt;/PropertyGroup&gt;

  &lt;!-- Log the generated package version if any --&gt;
  &lt;Message Text="Generated version '$(PackageVersion)'" Importance="high" /&gt;
 &lt;/Target&gt;
&lt;/Project&gt;</pre>

<p>I do hope TFS11 will bring a nice build template supporting this out-of-the-box, because this is exactly the kind of friction that I'd like to get rid of.</p>

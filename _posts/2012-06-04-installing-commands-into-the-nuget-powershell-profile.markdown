---
layout: post
title: "Installing commands into the NuGet PowerShell profile"
date: 2012-06-04 21:54:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/06/04/installing-commands-into-the-nuget-powershell-profile/"]
author: Xavier Decoster
redirect_from:
 - /2012/06/04/installing-commands-into-the-nuget-powershell-profile/.html
 - /2012/06/04/installing-commands-into-the-nuget-powershell-profile/.html
---
<p>If you're one of those guys who prefers to automate things than repeating them manually, you've probably spent some time already in the NuGet Package Manager Console. Many packages are providing way more than just a set of binaries. Some of them are really full installations of functionality and help you automate actions within Visual Studio. A good example is the well-known <a href="http://nuget.org/packages/MvcScaffolding" target="_blank">MvcScaffolding</a> package, which is providing tons of extra functionality in the PowerShell-enabled console.</p>

<h2>Building a tools package?</h2>

<p>There are also the so called <strong>tools packages</strong>, which only contain a tools folder inside the package and thus only ship such functionality, without installing anything into the target project or solution. If you want to build such package, it might be interesting to know that <a href="http://docs.nuget.org/docs/start-here/Using-the-Package-Manager-Console#Setting_up_a_NuGet_Powershell_Profile" target="_blank">NuGet supports its own PowerShell profile</a>.</p>

<p>An example tools package I recently created is the <a href="https://github.com/myget/NuGetPackages/tree/master/NuSpec" target="_blank">NuSpec</a> package (<a href="http://nuget.org/packages/NuSpec" target="_blank">Install-Package NuSpec</a>). It provides a bunch of PowerShell command-lets that help you automate the creation of .nuspec files within Visual Studio.</p>

<p>Installing this package does not change anything to your solution or project. It's only when you start using the command-lets that you modify these files. So why would we need to install this package for every single solution over and over again? Let's install it into the NuGet PowerShell profile and have it sit there, readily available for whenever you need it, no matter which solution you open.</p>

<h2>Installing the tools once and for all (*)</h2>

<p>(*) until the next update or something :)</p>

<p>The NuGet PowerShell profile is not always available on the consuming computer. So as a package producer, it is our job to make detect it and make sure it's there for us in order to import our own modules into it. For this, we can conveniently make use of the $profile variable which contains the path to the <strong>NuGet_profile.ps1</strong>, which should be available in <em>%USERPROFILE%\My Documents\WindowsPowerShell</em>.</p>

<p>The following piece of PowerShell code creates the file if it doesn't exist, and imports a module with a configurable name:</p>

<pre class="brush: powershell;auto-links:false;"># Set the module name
$moduleName = "MyModule"

# Check if the NuGet_profile.ps1 exists and register the module
if(!(Test-Path $profile)){
  mkdir -force (Split-Path $profile)
  New-Item $profile -Type file -Value "Import-Module $moduleName"
}
else{
  Add-Content -Path $profile -Value "`r`nImport-Module $moduleName"
}</pre>

<p>Put this in the <strong>init.ps1</strong> file of your NuGet package and copy all the required files from your tools folder to the *%USERPROFILE%\My Documents\WindowsPowerShell\Modules\MyModule* directory.</p>

<p>A simple convention here can save you some code: the name of the module is also the name of the directory to copy to in the Modules folder. The following example illustrates what I mean:</p>

<ul>
<li>NuGet package ID: <strong>MyModule</strong></li>
<li>PowerShell module and manifest inside tools folder: <strong>MyModule</strong>.psd1 and <strong>MyModule</strong>.psm1</li>
<li>Target copy location for required files: %USERPROFILE%\My Documents\WindowsPowerShell\Modules*<em>MyModule</em>*\</li>
</ul>

<p>Below, you find some key excerpts from the init.ps1 I use for the NuSpec package:</p>

<pre class="brush: powershell;gutter:false;toolbar:false;">param($installPath, $toolsPath, $package, $project)

# Configure
$moduleName = "NuSpec"

# Derived variables
$psdFileName = "$moduleName.psd1"
$psmFileName = "$moduleName.psm1"
$psd = (Join-Path $toolsPath $psdFileName)
$psm = (Join-Path $toolsPath $psmFileName)

# Check if the NuGet_profile.ps1 exists and register the NuSpec.psd1 module
if(!(Test-Path $profile)){
  mkdir -force (Split-Path $profile)
  New-Item $profile -Type file -Value "Import-Module $moduleName"
}
else{
  Add-Content -Path $profile -Value "`r`nImport-Module $moduleName"
}

# Copy the files to the module in the profile directory
$profileDirectory = Split-Path $profile -parent
$profileModulesDirectory = (Join-Path $profileDirectory "Modules")
$moduleDir = (Join-Path $profileModulesDirectory $moduleName)
if(!(Test-Path $moduleDir)){
  mkdir -force $moduleDir
}
copy $psd (Join-Path $moduleDir $psdFileName)
copy $psm (Join-Path $moduleDir $psmFileName)

# Copy additional files
...
copy "$toolsPath\*.xsd" $moduleDir
copy "$toolsPath\*.xml" $moduleDir
...

# Reload NuGet PowerShell profile
. $profile</pre>

<p><em>Note: This approach doesn't support the classical Update-Package or Uninstall-Package commands obviously. You'll have to detect these upgrades/migrations when installing your package, which, again, is nothing more than some file or file contents manipulation using PowerShell.</em></p>

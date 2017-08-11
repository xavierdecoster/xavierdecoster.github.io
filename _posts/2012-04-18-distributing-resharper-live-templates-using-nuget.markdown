---
layout: post
title: "Creating ReSharper Live Templates & distribute them using NuGet"
date: 2012-04-18 21:32:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2012/04/18/distributing-resharper-live-templates-using-nuget/"]
author: Xavier Decoster
redirect_from:
 - /2012/04/18/distributing-resharper-live-templates-using-nuget/.html
 - /2012/04/18/distributing-resharper-live-templates-using-nuget/.html
---
<p>In one of my recent projects I created some <a href="http://www.jetbrains.com/resharper/features/code_templates.html" target="_blank">ReSharper Live Templates</a> in order for other developers to easily create some code using a predefined class template. Live templates can significantly speed up the implementation of such classes, because developers only need to provide some minimal information (such as the class name and maybe some generic type parameters and such). There€™s also less room for accidental copy-pasting. Yeah, pressing CTRL+V after CTRL-C should be mapped to some macro that makes Visual Studio crash! :)</p>

<h2>Example use case</h2>

<p>But seriously, <a style="font-style: italic;" href="http://www.jetbrains.com/resharper/features/code_templates.html#File_Templates" target="_blank">file templates</a> can come in quite handy. Consider the following piece of code that needs to be converted into a ReSharper file template. It represents a custom Comparer implementation for a given contract that has a unique key. It's exported using a custom MEF export attribute that exposes some metadata. Such design is quite common for instance when some application dynamically loads assemblies containing such comparers and needs to find the best matching comparer for a given contract.</p>

<pre class="brush: c#;">[Export(typeof(MyContractComparer))]
[ContractComparer(typeof(MyContract))]
public partial class MyContractComparer : KeyComparer&lt;MyContract, long&gt;
{

  protected override Expression&lt;Func&lt;MyContract, long&gt;&gt; KeyProperty
  {
    get { return contract =&gt; contract.Id; }
  }
}</pre>

<p>Imagine that various of those classes need to be implemented, with potentially a different base type and more comparison logic, but in the end they're all minor differences in implementation.</p>

<h2>Exploring ReSharper's Templates</h2>

<p>Here's where templates come in handy. Creating such file template in ReSharper is pretty straightforward and reveals some cool options, such as type completion and smart snippet completion. ReSharper comes with a built-in <em>Templates Explorer</em> which you'll find in the menu under <strong>ReSharper > Templates Explorer</strong>.</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2104.png" alt="" /></p>

<p>In the dialog that opens, you'll notice 3 tabs. I want to create a C# file template so the <em>File Templates</em> tab is the one we need. You'll notice the 4 default C# file templates provided by ReSharper out-of-the-box when you click on C# on the left. These are the templates used when right clicking a project and selecting <strong>Add > New from Template > ...</strong></p>

<p><img style="max-width: 650px;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2109.png" /></p>

<h2>Creating the File Template</h2>

<p>That's where my new File Template needs to appear, so let's create a new one. I'll call it KeyComparer (click on the image for a high-res view). The description is what you'll see in the right-click context menu above. The default file name needs to include the extension. Notice that this is purely to indicate ReSharper that it should create (in this case) a C# file using the .cs extension. You won't see this in the dialog that will prompt you for the name of the KeyComparer when using the File Template.</p>

<p><img src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2145.png" alt="" /></p>

<p>You'll notice immediately that there are some placeholders marked in the code window on the left, which also appear in the variables list on the right. Here's where the real magic kicks in: you can assign <em>macros</em> to each of these placeholders and select which ones are editable or not (and even which occurance if the placeholder occurs multiple times in the same template). You see the placeholders in the <span style="color: #ff0000; font-weight: bold;">red</span> box? Those are the only ones that the developer will need to provide when using this template while creating this class. That's like: provide a name, use autocomplete + tab key three times, save the file and done! In this sample use case, you simply provide the type you want to implement the comparer for, you provide the type of the KeyProperty, and you select the property on the compared type that represents the unique key of the object.</p>

<p><a href="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2131.png" target="_blank"><img style="max-width: 650px;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2131.png" /></a></p>

<p>There are quite a lot of macros available which allow you to do a lot of neat stuff (some macros can use other variables or contextual information of the file you create). Just play with it and discover for yourself. Don't forget to add the newly created template to the quicklist by dragging and dropping it.</p>

<table border="0">
  <tbody>
    <tr>
      <td style="text-align: center;">
        <img alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2143.png" />
      </td>

      <td style="text-align: center;">
        <img alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2144.png" />
      </td>
    </tr>
  </tbody>
</table>

<h2>Distribute the template in a NuGet package</h2>

<p>It's very cool if you have a set of templates you find useful, but why would you keep them for yourself? This particular template for instance could be considered part of the product that contains the <em>base type</em>. Guess what: the assembly containing these base types and MEF attributes is being shipped as a NuGet package. So why not make the package smarter and embed these ReSharper templates and install them into the consuming target solution?</p>

<p>ReSharper support <em>layers</em> of settings, and one of them is on the solution level. This is the <em><solutionName>.DotSettings</em> file you often find in your code repository. This basically is an XML file, and NuGet supports XML transforms (well, it's called config transforms but it works on any XML file). Great, but sadly enough, I don't know the filename (or solution name) upfront so I cannot take benefit from that. I still had to figure out how to find the name of the target solution and a way to perform this transformation upon installation. If the target file doesn't exist yet, we need to copy and rename our source file. If it does exist, we need to somehow merge the two (for now, I'll only <strong>append</strong>). Luckily, there's PowerShell: any NuGet package can hook into its own installation using an <em>install.ps1</em> file in the Tools folder of the package. And how convenient: we get access to some handy variables amongst which <em>$dte</em> (Visual Studio DTE)!</p>

<p>That's enough information I think, here's the script that will take the file Tools\Comparers.Templates.DotSettings and install it:</p>

<pre class="brush: powershell;gutter:false;auto-links:false;">param($installPath, $toolsPath, $package, $project)

$resharperSettingsPath = [System.IO.Path]::Combine($toolsPath, 'Comparers.Templates.DotSettings')

$slnFullName = $dte.Solution.FullName
$teamsettingsFileName = $slnFullName + ".DotSettings"

if(-not([System.IO.File]::Exists($teamsettingsFileName))){
Copy-Item -Path $resharperSettingsPath -Destination $teamsettingsFileName -ErrorAction stop
}
else{
    $newContents = (Get-Content $resharperSettingsPath);
    $newContents = $newContents -Replace "&lt;wpf:ResourceDictionary xml:space=""preserve"" xmlns:x=""http://schemas.microsoft.com/winfx/2006/xaml"" xmlns:s=""clr-namespace:System;assembly=mscorlib"" xmlns:ss=""urn:shemas-jetbrains-com:settings-storage-xaml"" xmlns:wpf=""http://schemas.microsoft.com/winfx/2006/xaml/presentation""&gt;", "";
    (Get-Content $teamsettingsFileName)| Foreach { $_ -Replace "&lt;/wpf:ResourceDictionary&gt;", "$newContents" }| Set-Content $teamsettingsFileName;
}

Write-Host "Successfully installed Resharper Live Templates into '$teamsettingsFileName'"</pre>

<p>Note: there's likely more to it to properly update and uninstall these templates as part of the NuGet package, and I'm not saying the merge/append process is ideal (proper XML processing would be better), but I'll tackle that problem when it occurs. For now, you can at least distribute your templates together with the NuGet package they belong to. If you have no issue with overwriting the entire file, then this solution will do.</p>

<p>Feel free to post your solution :)</p>

<p><img style="max-width: 650px;" alt="" src="https://xavierdecosterblog.blob.core.windows.net/blog/2012-04-18/2012-04-18_2258.png" /></p>

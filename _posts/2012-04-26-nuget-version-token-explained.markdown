---
layout: single
title: "NuGet $version$ token explained"
date: 2012-04-26 21:38:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["NuGet","Package Management"]
alias: ["/2012/04/26/nuget-version-token-explained/"]
author: Xavier Decoster
redirect_from:
 - /2012/04/26/nuget-version-token-explained/.html
 - /nuget-version-token-explained
---
Many questions related to creating <a href="https://www.nuget.org" target="_blank">NuGet</a> packages are related to versioning. 
One question in particular I'd like to discuss here because it's easier to point people to a blog post. 
The question is: 

> **How do I use the *$version$* token in the NuGet manifest (nuspec) file? Where does it get the version number from?**

If you look at the <a href="http://docs.nuget.org/docs/reference/nuspec-reference#Replacement_Tokens" target="_blank">NuGet docs explaining the nuspec replacement tokens</a>, it states the following - if it doesn't, my <a href="https://github.com/NuGet/NuGetDocs/pull/27" target="_blank">pull request</a> got accepted :)

> The assembly version as specified by the assembly's `AssemblyVersionAttribute`.

That is not entirely accurate. 
Consider the following `AssemblyInfo.cs` file contents:

```
[assembly: AssemblyVersion("1.0.0.0")]
[assembly: AssemblyFileVersion("1.0.0.0")]
[assembly: AssemblyInformationalVersion("1.0.2.0")]
```

This is where most people get confused. 
I won't get into the details of which version attribute you should or should not use, as there can be good reasons to use either one of them in different scenarios. 
I'll focus on how NuGet is using this information to provide a version number to the `$version$` replacement token in the nuspec file.

Building a NuGet package using a tokenized nuspec file that relies on assembly information can be achieved in various ways, for instance:

* `nuget spec <csproj>` to generate the tokenized nuspec, followed by `nuget pack <csproj>`
* `nuget spec -a <assemblyPath>` inside the `.csproj` folder to generate a non-tokenized nuspec (with metadata already filled in), followed by `nuget pack <nuspec>`

Basically, it comes down to this:

* If the `AssemblyInformationalVersion` attribute is available, then that one is used.
* If the `AssemblyInformationalVersion` attribute is not available, then the `AssemblyVersion` attribute is used instead.
* If none of the above are specified, your assembly will have a version number of `0.0.0.0`, as well as the resulting package.
* NuGet completely ignores the `AssemblyFileVersion` attribute.

Note that this behavior is the same when skipping the `.nuspec` at all and building a NuGet package directly from an assembly, using `nuget pack <assembly.dll>`.
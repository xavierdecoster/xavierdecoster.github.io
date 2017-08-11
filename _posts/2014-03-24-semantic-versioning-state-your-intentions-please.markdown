---
layout: single
title: "Semantic Versioning: State your intentions please!"
date: 2014-03-24 00:00:00 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2014/03/24/semantic-versioning-state-your-intentions-please/"]
author: Xavier Decoster
redirect_from:
 - /2014/03/24/semantic-versioning-state-your-intentions-please/.html
 - /2014/03/24/semantic-versioning-state-your-intentions-please/.html
---
<p><p>I'm quite a fan of Semantic Versioning (<a href="http://www.semver.org">SemVer</a>). It is the most pragmatic approach towards software versioning that I know of. If you don't know about SemVer, then you should go read the spec now. However, it is not perfect. Mostly because people are not perfect and some aspects of the specification can be interpreted differently. Actually, the spec itself is pointing this out – and I quote:
</p>
<blockquote><p><span style="font-family:Arial; font-size:10pt">For this system to work, you first need to declare a public API. This may consist of documentation or be enforced by the code itself. Regardless, it is important that this API be clear and precise. Once you identify your public API, you communicate changes to it with specific increments to your version number.
</span></p>
</blockquote>
<p>Now, that's a big judgement call, isn't it? What is a public API? How do you define it in such way that it is clear to everyone without room for interpretation?
</p><h1>Recap: the semantic version format
</h1><p>I'm not going to explain the spec in detail here, but I want to quickly recap the most fundamental basics of it. SemVer has three version parts expressing semantics.
</p><p><pre>major.minor.patch</pre>
</p><p>I'm ignoring the metadata that can be appended (such as build stamps etc). It's just metadata after all, so not important in this context. Instead, I'm going to focus on the semantics here, because that's really what this spec is all about: semantics and intent. These three version parts are indicators of the author's intentions:
</p><ul><li>Major: incrementing this version part means that the author intends to indicate a breaking change in the public API (I'll try to define this public API later in this post)
</li><li>Minor: incrementing this version part means that the author intends not to break anything (and truly thinks he won't), but backwards compatible additions or changes to the public API have been introduced (at least, the intent here is that these things are backwards compatible)
</li><li>Patch: incrementing this version part means that the author intends not to break anything, and believes no changes to the public API happened. Furthermore, the author thinks you can safely upgrade to the latest patch release without any risks as changes are internal.
</li></ul><h1>Defining the public API
</h1><p>As simple as the SemVer format may sound, the devil's in the details. How do you assess that your intentions match with the changes you may have introduced in your new release?
</p><p>The first time you ever read the spec and start thinking about your <em>public API</em>, you'll likely limit your thinking to your code base and the public types and methods exposed in your libraries. Now that's obviously a good start, but reality is a little bit more complex. Let's assess the impact of the following, and these are real questions people ask themselves (rightfully!):
</p><ul><li>What if I don't change anything in my code base and only upgrade one of my dependencies?
</li><li>What if the internal behavior of a public method on a public type changed, but did not affect its signature? (eg: public bool Type.IsSecure now returns false instead of true)
</li><li>What if a type moved to another assembly without changing namespace or any of its publicly visible members? (so only the "packaging" of this type changed)
</li></ul><p>You can already tell that a <em>public API</em> likely shouldn't be defined solely by signatures and type visibility.
</p><p>First and foremost: this is just my personal perspective on things. This is what works for me and hopefully as well for anyone consuming the API's I create. If it doesn't work for you, then please don't hesitate to tell me why in the comments. If you have a better approach, then please share it with the world so I can learn from you.
</p><h2>SemVer is technology agnostic
</h2><p>SemVer is a set of pragmatic rules and tries to provide guidelines. However, it is also technology agnostic. On purpose! As much as I believe that the guidelines set forth in this spec are valuable, I also believe there's a need to define a standard definition of <em>public API</em> within the scope of a certain technology.
</p><p>In .NET, we have a long history of looking at assemblies as the release vehicle. History shows us that versioning assemblies can quickly become problematic, and the words <em>dependency hell</em> may come to mind. That's mainly because a release vehicle also brings technical limitations to the way your API can and will be referenced.
</p><ul><li>You apply strong naming? Why does that even exist? Oh, right, the Global Assembly Cache… Wait a minute, isn't that a Windows thing? Why should my <em>public API</em> care about that?
</li><li>You remember .NET Remoting? Then you're probably familiar with the <a href="http://msdn.microsoft.com/en-us/library/aa302331.aspx">breaking aspects</a> of changing version numbers...
</li><li>Moving a type to another assembly requires consumers to recompile against your new assemblies, so again: breaking (even without any changes to the API of the type being moved)!
</li></ul><p>And then along came NuGet! Another release vehicle. Some packages contain a single assembly, some contain multiple assemblies, and some contain none at all! This is where many people lose faith in SemVer and start asking questions similar to the ones I mentioned earlier in this post. In fact, the SemVer FAQ and issue list on GitHub is full of those questions and related discussions.
</p><h2>My definition for .NET development
</h2><p>I'm trying to define a strict public API in an attempt that anyone consuming it has a clear understanding of what to expect. I'll restrict my definition to any .NET API that ships as an assembly (or set of assemblies) through NuGet. Why? Because I'm taking into account any changes to the release vehicles into the versioning strategy of my public API.
</p><p>Hence, in my definition for .NET development, a <em>public API</em> is defined by:
</p><ul><li>The <strong>public types, members and method signatures</strong> exposed in the consumable components
</li><li>The <strong>behavior</strong> of those types and members (if the <em>semantics</em> of an unchanged method signature change, that's breaking)
</li><li>The <strong>assembly qualified name</strong> of these types: did the type change assembly? (if so, that's breaking)
</li><li>The <strong>assembly signing</strong>: <a href="http://haacked.com/archive/2012/02/16/changing-a-strong-name-is-a-major-breaking-change.aspx/">changing SNK is breaking</a>! (and why the hell are you still signing assemblies? Can't we just stop doing that and move on?)
</li><li>The <strong>packaging</strong> of these assemblies: did the assembly change NuGet package? (if so, that's breaking)
</li></ul><p>I'm not sure whether I'm overlooking any other aspects of the public API, but using the above interpretation hasn't led me into discovering any. This definition also really helps me to clearly state my <em>intentions</em> when defining the semantic version vNext of any public API I own.
</p><h1>The sad part
</h1><p>The sad part about SemVer is that you can't interpret an author's intentions solely based on the version format. It's not because your versioning format happens to comply with the guidelines set forth by SemVer that you are using SemVer properly. If you're a log4net user, <a href="http://www.wiktorzychla.com/2012/03/pathetic-breaking-change-between.html">you might have been bitten by this before</a>.
</p><p>Or perhaps you jumped from v4.0 to v4.5? (Looking at you .NET Framework <span style="font-family:Wingdings">J</span>) This is clearly an example of what I would call Marketing Driven Versioning. Technically, the .NET Framework doesn't apply SemVer (and no one ever claimed it does). In fact, most (if not all?) .NET 4.5 libraries have a version number of v4.0 (remember, it's an in-place upgrade). However, people get confused as the version format <em>seems</em> to be SemVer-compliant.
</p><h1>Moving forward
</h1><p>Moving forward, I really hope to see NuGet be fully SemVer compliant one day (I know it won't be trivial as there's quite a legacy of non-SemVer compliant packages on the Gallery). But even if all new packages would be enforced to be SemVer-compliant, I'd argue they're likely just SemVer-<strong>format</strong> compliant. And that's totally fine from a NuGet perspective as there's nothing more that can be done about that!
</p><p>At the same time, that's why I'd love to see a <em>standard definition of public API</em> for anything that ships through NuGet. People need guidance and preferably this guidance is specific to their needs/scenario. The version format alone simply doesn't cut it: it doesn't say that it was the author's intention to be SemVer-compliant and as such, the version format alone doesn't clearly state the author's intentions with any new release.
</p><p>If there is any metric I want to know about a package, then it is not the download count, but rather the number of times a package author has broken SemVer-compliance for a given package. Not sure how to solve that. Perhaps people should be able to report that a package upgrade from one version to another broke their stuff? Correlate the number of reports against the number of <em>package-updates</em> (not installs, not restores …)?
</p><p>Anyway, I don't have control over any of this (and likely no one has), but I believe guidance is critical, and I hope someone finds value in this way of working. SemVer is awesome! I understand the reasoning behind it being agnostic on some levels. We should just state our intentions. I'd be happy even if just a single package author finds value in this post and improves his versioning strategy. Note that the goal is to propagate this happiness to all package consumers ;)</p></p>

<p>SemVer is not about code, it's about stating your intentions...</p>

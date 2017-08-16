---
layout: single
title: "Semantic Versioning & auto-incremented NuGet package versions"
date: 2013-04-29 00:00:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["ALM","NuGet","SemVer","Package Management"]
alias: ["/2013/04/29/semantic-versioning-auto-incremented-nuget-package-versions/"]
author: Xavier Decoster
redirect_from:
 - /2013/04/29/semantic-versioning-auto-incremented-nuget-package-versions/.html
 - /2013/04/29/semantic-versioning-auto-incremented-nuget-package-versions/.html
---
<p><p>Any NuGet package is uniquely identified by a <em>package ID</em> and a <em>version</em>. If you've been to one of my talks or follow my blog, you know I'm quite a fan of <a href="http://semver.org">semantic versioning</a>. I even wrote <a href="/nuget-package-analysis-encouraging-semantic-versioning">a NuGet package analysis rule</a> for it, which is now also built-in as <a href="http://blog.myget.org/post/2013/03/16/Require-semantic-versioning-for-packages-pushed-to-your-feed.aspx">a MyGet feed setting</a>. Versioning a NuGet package is fairly easy – stamping a NuGet package with a version, how hard can it be? – but when applied <em>continuously</em> as part of your automated builds, it might mess with your mind at some point.
</p><h1>Contradictio in terminis: auto-versioning a yet unknown semantic version
</h1><p>If you apply Continuous Integration on NuGet packages (I call it <a href="/post/2011/07/18/continuous-package-integration-nuget-vs-source-control">Continuous Package Integration</a>), you are basically producing NuGet packages as part of your automated build process. These packages ideally end up on a NuGet repository specifically meant for CI/development purposes. This concept is a core feature of <a href="http://www.myget.org/">MyGet.org</a>: you can <a href="http://blog.myget.org/post/2013/03/22/Whats-new-in-Build-Services.aspx">connect</a> for instance your GitHub repository with our build services and continuously produce your NuGet packages upon each commit. They'll be hosted on the connected MyGet feed, and from there you can push them upstream to any other <a href="http://www.nuget.org">NuGet</a> feed (or yet another MyGet feed). To be able to host these continuously build packages, you should version them uniquely, e.g. add a build stamp! No complex logic there: use an auto-incremented number or a datetime-stamp for instance. You can automate this easily.
</p><p>On the other hand you have <a href="http://semver.org">Semantic Versioning</a> which is a very pragmatic approach towards versioning: it gives a meaning (semantics) to each version number. The major, minor and patch version numbers are defined based on the changes you <em>did</em> between two releases. In other words: you only know for sure what changes you did until you have frozen the code base, which actually happens at release-time (I mean, why delay it further?).
</p><p>The contradiction is hitting you right in your face at this point:
</p><ul><li>Auto-increment a build stamp (part of the version number)…
</li><li>… on a yet unknown semantic version? Wait! What?
</li></ul><p>How can I auto-increment an unknown version?
</p><h2>Adding NuGet to the mix
</h2><p>Now, if the above is causing you trouble, it gets even more complicated. The following is a slide I use in my anti-patterns talk which sums it up in a single overview. The "legacy" versioning scheme mentioned on the slide is the one you'll find on <a href="http://msdn.microsoft.com/en-us/library/51ket42z(v=vs.110).aspx">MSDN</a>.
</p><p><img src="/images/2013-04-30/nuget_semver_comparison.png" alt="" style="max-width:600px;"/>
    </p><p>That's right: NuGet – at this point – does not <em>fully</em> support SemVer yet. I know it is coming and when it is there you'll get a new post from me, promised! But for now, you need to be aware of where the NuGet versioning algorithm differs from SemVer.
</p><h1>But I want to do this the "right" way!
</h1><p>So do I! I think – for now – there is not really a <em>right</em> way, at least it doesn't feel like that to me. The latest SemVer spec has improved guidance in terms of adding <em>metadata</em>. A build stamp actually is just a piece of metadata, it has <em>no semantics</em> whatsoever. It makes your build unique, and you can maybe trace it back to your VCS commits or work items. But it doesn't mean anything to the consumer of your package! Lovely approach, I couldn't agree more!
</p><p>As long as NuGet doesn't support this spec to its full extent, it just doesn't feel <em>right</em>. However, the approach I explain below doesn't feel <em>wrong</em> either. It is a little bit of a hack, I agree: I'll be abusing the pre-release tag for this. But CI packages aren't full releases anyway, are they? Only if you decide to <em>promote</em> a given CI package to the release repository and strip off the pre-release tag, you end up with a release (note: no recompilation). Actually, you <em>don't have to</em> strip it from its pre-release tag, you're free to promote pre-releases for consumption as well. I'm not violating any of this in my approach for CI package versioning.
</p><h2>Why don't you use the revision number for CI?
</h2>

<blockquote><p>"Oh, I know, let's add a 4<sup>th</sup> number – the revision number – this is perfectly fine <a href="http://msdn.microsoft.com/en-us/library/51ket42z(v=vs.110).aspx">according to MSDN</a>!"
</p>

</blockquote>

<p>Please… don't! Either you apply SemVer, or you don't. SemVer doesn't have a revision number. Actually, <a href="http://msdn.microsoft.com/en-us/magazine/jj851071.aspx">using the revision number for NuGet packages is an anti-pattern</a> in my book.
</p><p><img src="/images/2013-04-30/nuget_semver_comparison2.png" alt="" style="max-width:600px;"/>
    </p>

<blockquote><p>"But NuGet supports it!"
</p>

</blockquote>

<p>Yep, but are you aware that a version with a revision number is considered higher than a version without a revision number? Once there is a package with a 4<sup>th</sup> version number in you repository, your repository is no longer SemVer-compliant. A package with version 1.2.1.20130429 will always be considered newer than 1.2.1. I'm sure you like a proper release version and prefer 1.2.1 for the actual release? Also, you can't include any dots in the pre-release tag. Oh, and it's not even supported by SemVer, which is the topic of this post!
</p><p>As stated earlier, your package consumer doesn't care about the build stamp, it's meaningless to them, so why would you bother them (and yourself?) with it?
</p><h2>Change the package ID!
</h2><p>Again, don't! It's like changing the assembly name instead of the version. And these packages can co-exist on the same repository! And before you come up with anything else, please: <a href="http://msdn.microsoft.com/en-us/magazine/jj851071.aspx">read my NuGet (anti-)patterns article on MSDN</a>
        <span style="font-family:Wingdings">J</span>
    </p><h2>Hijacking the pre-release tag
</h2><p>No revision number then. What else do we have: the pre-release tag. Let's abuse it in a righteous way: let's add the build metadata to the pre-release tag. The pitfall here is to make sure we don't brake package sorting:
</p><ul><li>Datetimestamps are easy if you have a fixed format: you can come up with something like –pre201304291755 (heh, 24h time scheme proves useful after all <span style="font-family:Wingdings">J</span>)
</li><li>Auto-incrementing number is a little harder, but still possible. I'll explain this one below.
</li></ul><p><strong>Fact: NuGet sorts pre-release tags alphabetically.</strong> (Technically: <a href="http://docs.nuget.org/docs/Reference/Versioning">in lexicographic ASCII sort order</a>)
</p><p>This effectively means that the following package precedence definition is true: 1.0.0-alpha2 &gt; 1.0.0-alpha10. Oops! We need leading zeros: 1.0.0-alpha02 &lt; 1.0.0-alpha10. Much better. But wait, how do I know how many leading zeros I need? Answer: you don't! Pick a number which is high enough: do you make 100 builds between two releases? 1000? More? Here's the thing: MyGet build services does this for you. No need to worry about the leading zeros, we use 5 digits for the auto-increment by default. You need more? Add another one in front of the placeholder and change it manual if you max out. Note that we will improve this further as soon as full SemVer support is available in NuGet.
</p><p><img src="/get/042913_1149_SemanticVer3_635028329532561185.png" alt=""/>
    </p><h1>But how do I decide what SemVer to use for vNext?
</h1>

<blockquote><p>"I just started working on vNext after my 0.11.1 release. What semantic version should I use for my CI builds?"
</p>

</blockquote>

<p>The minimum required SemVer version increment is a patch release: start from <strong>0.11.2</strong>-CI00001 and see from there. Also, it's totally fine to increment the SemVer part between 2 sequential pre-releases, e.g. 0.11.2-CI00001 --&gt; 0.12.0-CI00001. Just take into account that only a single SemVer number can be incremented by a single digit between two releases: either you increment the major, the minor or the patch version by 1 (so from 0.11.1 to 0.12.0 is OK, from 0.11.1 to 0.12.1 is not).
</p><p>So you start working on vNext?
</p><ul><li>Reset the build counter
</li><li>Increment the patch number (or the minor or major number, it depends how you look at a release being a hotfix or adding functionality, I tend to take a pessimistic approach here as I can always increment but never decrement)
</li></ul><p>You can apply these principles in any CI tool, it's up to you which one you pick. From experience I can tell you TeamCity is very flexible in supporting this, for TFS you'll need to customize the workflow or go MSBuild all the way. Furthermore, all of the concepts explained in this post are fully supported on MyGet: <a href="http://blog.myget.org/post/2013/03/06/MyGet-Build-Services-Package-Versioning-Explained.aspx">there's a how-to on our blog</a>.
</p><p>Happy packaging!</p></p>

---
layout: single
title: "Gently phase out support for older NuGet packages with MyGet"
date: 2011-11-13 20:29:00 +0100
comments: true
published: true
categories: ["post"]
tags: ["ALM","NuGet","MyGet","Package Management"]
alias: ["/2011/11/13/gently-phase-out-support-for-older-nuget-packages-with-myget-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2011/11/13/gently-phase-out-support-for-older-nuget-packages-with-myget-aspx/.html
 - /2011/11/13/gently-phase-out-support-for-older-nuget-packages-with-myget-aspx/.html
---
<p>Simply deleting those packages is most likely not the best option. You probably have no idea of who still depends on them, either directly or through another package depending on it, even though you could try to <a href="/post/2011/10/06/Generate-package-dependency-matrix-directly-from-TFS-source-control.html" target="_blank">analyze your source control repositories</a> and get some first insights.</p>

<p><a href="http://www.myget.org" target="_blank">MyGet</a> now has a new feature that allows you to indicate whether or not you still support a given package.</p>

<h1>Toggle Package Support</h1>

<p>When navigating to the package details page on MyGet, you'll notice that a new button has been added next to each entry in the package history listing, as shown below.</p>

<p><a href="/images/2011-11-13/package_history.png" target="_blank"><img width="650" height="174" alt="" src="/images/2011-11-13/package_history.png" /></a></p>

<p>You can easily toggle the state of a package by marking it as listed or unlisted. We'll also alert you about the impact of your change so you can't break anything by accidentally clicking the button.</p>

<p><img src="/images/2011-11-13/alert_make_unlisted.png" alt="" /></p>

<h1>Package Listing Explained</h1>

<p>By default, all new packages are listed obviously, otherwise you wouldn't be able to download the package from the feed. However, we want to give you the ability to explicitly <em>unlist</em> a package from your feeds. By unlisting a package, you indicate you no longer wish to support that specific version of a package. In essence, you want to <strong>give people a gentle push towards upgrading</strong> to a newer version. That's exactly the goal of this feature.</p>

<p>Unlisting a package is not the same as permanently deleting a package from the feed. You could look at it as performing a <strong>soft delete</strong>. This also means that this operation <strong>can be undone</strong>! The operation of listing or unlisting a package is restricted to <em>feed owners, feed co-owners *and *feed contributors</em>. If you only have <em>package contributor</em> permissions for the feed, you also have to be the explicit <em>package owner</em>.</p>

<p>The reason we require these permissions are obvious: this is a breaking change in your package workflow! If you are using a <a href="/post/2011/07/18/Continuous-Package-Integration-NuGet-vs-Source-Control.html" target="_blank">no-checkin policy for NuGet packages</a>, make sure you inform people to check-in this package into their VCS so you don't break their stuff.</p>

<p>Be a good citizen when using this feature. In a controlled enterprise environment, this should be easy.</p>

<p style="background-color: #ccc; border: 1px solid #000; padding: 5px;">
  Remember: upgrading a dependency is a consumer responsibility, so it's the consumer's decision <em>when</em> to upgrade.
</p>

<p>This feature is all about <strong>discontinuing a NuGet package while maintaining an upgrade path</strong>.</p>

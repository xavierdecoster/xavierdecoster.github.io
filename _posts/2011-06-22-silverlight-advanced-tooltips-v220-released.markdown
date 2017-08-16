---
layout: single
title: "Silverlight Advanced ToolTips v2.2.0 released"
date: 2011-06-22 19:07:00 +0200
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/2011/06/22/silverlight-advanced-tooltips-v220-released/"]
author: Xavier Decoster
redirect_from:
 - /2011/06/22/silverlight-advanced-tooltips-v220-released/.html
 - /2011/06/22/silverlight-advanced-tooltips-v220-released/.html
---
<p>This release mainly contains bugfixes for issues reported by users of the library (and a big thanks to all those who even bothered supplying me with a patch!).</p>

<p>Here's the changelog for v2.1.1 --&gt; v2.2.0:</p>

<ul style="margin-left: 0em; padding-left: 2em; list-style-type: none; list-style-image: url('http://i2.codeplex.com/Images/v17889/doublearrow.gif');">
<li style="margin-left: 0px; margin-bottom: 0.3em; margin-top: 0.3em; vertical-align: middle;"><span style="text-decoration: underline;">bugfix</span>: fixed a memory leak issue</li>
<li style="margin-left: 0px; margin-bottom: 0.3em; margin-top: 0.3em; vertical-align: middle;"><span style="text-decoration: underline;">bugfix</span>: fixed an internal NullReferenceException on ToolTipService.OnElementIsEnabledChanged</li>
<li style="margin-left: 0px; margin-bottom: 0.3em; margin-top: 0.3em; vertical-align: middle;"><span style="text-decoration: underline;">bugfix</span>: fixed an internal KeyNotFoundException on ToolTipService.OnElementIsEnabledChanged</li>
<li style="margin-left: 0px; margin-bottom: 0.3em; margin-top: 0.3em; vertical-align: middle;"><span style="text-decoration: underline;">bugfix</span>: now properly supports binding with Fallback value when binding directly against Tooltip attached property</li>
<li style="margin-left: 0px; margin-bottom: 0.3em; margin-top: 0.3em; vertical-align: middle;"><span style="text-decoration: underline;">new</span>: added read-only bindable public property "Owner" which is a reference to the FrameworkElement that owns the ToolTip</li>
</ul>

<p>You can find the full changelog history <a href="http://tooltipservice.codeplex.com/wikipage?title=ChangeLog" target="_blank">here</a>.</p>

<p>Of course, this release is now <a href="http://www.nuget.org/List/Packages/Silverlight.Advanced.ToolTips" target="_blank">also available on NuGet</a>, so go ahead and make a benefit of this awesome packaging system (you'll get update notifications if an updated package is made available in the future).</p>

<p>Enjoy!</p>

<p><img alt="" src="/images/2011-06-22/2011-6-nugetpackage_tooltipservice220.PNG" width="650" height="74" /></p>

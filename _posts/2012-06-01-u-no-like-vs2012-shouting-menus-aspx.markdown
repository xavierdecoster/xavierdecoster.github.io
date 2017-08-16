---
layout: single
title: "U NO LIKE VS2012 SHOUTING MENUS?!"
date: 2012-06-01 21:50:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["Visual Studio"]
alias: ["/2012/06/01/u-no-like-vs2012-shouting-menus-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2012/06/01/u-no-like-vs2012-shouting-menus-aspx/.html
 - /2012/06/01/u-no-like-vs2012-shouting-menus-aspx/.html
---
<h2>::: TO DISABLE ALL CAPS MENUS :::</h2>

<ul>
<li>Install-Package <a href="http://nuget.org/packages/VS2012.RemoveAllCaps" target="_blank">VS2012.RemoveAllCaps</a></li>
<li>Disable-AllCaps</li>
<li>Uninstall-Package VS2012.RemoveAllCaps</li>
</ul>

<p>The <strong>Disable-AllCaps</strong> and <strong>Enable-AllCaps</strong> cmdlets will still be available after uninstalling the package, as it is persisted into the NuGet PowerShell profile.</p>

<p>You might need to restart VS2012 in order for the changes to take effect.</p>

<p><a href="http://www.richard-banks.org/2012/06/how-to-prevent-visual-studio-2012-all.html" target="_blank">Credits to Richard Banks for the registry hack</a> :)</p>

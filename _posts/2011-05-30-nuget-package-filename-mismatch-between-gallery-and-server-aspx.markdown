---
layout: single
title: "NuGet package filename mismatch between gallery and server"
date: 2011-05-30 17:45:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["NuGet","Package Management"]
alias: ["/2011/05/30/nuget-package-filename-mismatch-between-gallery-and-server-aspx/"]
author: Xavier Decoster
redirect_from:
 - /2011/05/30/nuget-package-filename-mismatch-between-gallery-and-server-aspx/.html
 - /nuget-package-filename-mismatch-between-gallery-and-server
---
<p>Just noticed this one when setting up a corporate <a href="http://www.nuget.org" target="_blank">NuGet </a>server implementation based on NuGet.Server.dll.</p>

<p>NuGet (both commandline, vs extensions and package explorer) uses the following package filename convention:</p>

<p><em><span style="font-family: mceinline; font-size: 12pt;">$(packageID).$(version).nupkg</span></em></p>

<p>However, when you try to download a package (physically, e.g. for local/offline/archiving reasons), you'll notice there is a mismatch.</p>

<p>Try this one for instance: <a href="http://packages.nuget.org/v1/Package/Download/Ninject/2.2.0.0" target="_blank">http://packages.nuget.org/v1/Package/Download/Ninject/2.2.0.0</a> </p>

<p>Go ahead and try any other package/version combination available on the gallery.</p>

<p>Found it?</p>

<p>This is what you'll get:</p>

<p><em><span style="font-family: mceinline; font-size: 12pt;">$(packageID)<strong>-</strong>$(version).nupkg</span></em></p>

<p>If you want to use these packages to quickly test your own NuGet server implementation (which is what I did), you'll get a <strong>404 Not Found</strong>.</p>

<p><img src="/images/2011-05-30/2011-5-operation_failed.png" alt="" /></p>

<p>It took me a while to figure out what was wrong, because I was still able to see the package appear in the feed (you know you're probably wrong when you're trying to mess with the server config at this point).</p>

<p><img width="650" height="219" alt="" src="/images/2011-05-30/2011-5-pkg_listed.png" /></p>

<p>Solution: you'll have to <strong>rename</strong> them (replace dash with dot!)</p>

<p>Hope this helps someone!</p>

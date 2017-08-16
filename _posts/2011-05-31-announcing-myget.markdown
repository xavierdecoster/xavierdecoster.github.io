---
layout: single
title: "Announcing MyGet: NuGet-as-a-Service"
date: 2011-05-31 17:55:00 +0200
comments: true
published: true
categories: ["post"]
tags: ["MyGet","Community","Package Management"]
alias: ["/2011/05/31/announcing-myget/"]
author: Xavier Decoster
redirect_from:
 - /2011/05/31/announcing-myget/.html
 - /2011/05/31/announcing-myget/.html
---
<p>Ever since the announcement of <a href="http://www.nuget.org" target="_blank">NuGet</a> I've been intrigued by the potential it could bring to enterprises building software on the .NET platform (<a href="http://www.hanselman.com/blog/NuGetForTheEnterpriseNuGetInAContinuousIntegrationAutomatedBuildSystem.aspx" target="_blank">one could call them big boring companies</a>). I considered setting up a private NuGet server at both <a href="http://www.realdolmen.com" target="_blank">RealDolmen</a> (my employer) and one of our customers, and it happens to be that many colleagues found it a great idea, including <a href="http://blog.maartenballiauw.be" target="_blank">Maarten Balliauw</a>. We got talking during <a href="http://www.microsoft.com/belux/techdays/2011/" target="_blank">TechDays Belgium</a> about how useful this could be at our company, and at some customer locations, and if you know Maarten a bit, you can expect we ended up quite fast with our heads in the cloud!</p>

<p>An idea was born. Actually, we found it a <em>nice</em> (NaaS) idea! ^^</p>

<h2>NaaS!? Nice!</h2>

<p>Yep, you heard it! NuGet-as-a-Service!<a href="http://www.myget.org" target="_blank"><img alt="" align="right" src="http://www.myget.org/Content/images/myget/logo.png" /></a>What about having some site in the cloud where you could just create your own feeds with your private packages?</p>

<p>What if you could create a private feed for company components and share that with your colleagues, without the need to get a server, reserve some app-pool, create your NuGet repository, ...</p>

<p>You can come up with some awesome features that could make this idea into a product that could work for anyone.</p>

<p>To get us started in this direction, we bring you <a href="http://www.myget.org" target="_blank">MyGet</a>!</p>

<p>MyGet is a NuGet server hosted on <a href="http://www.microsoft.com/windowsazure/" target="_blank">Windows Azure</a> that allows you to create and manage your own private NuGet feeds. </p>

<p>Just register an account and start creating your own private named feeds.</p>

<p><img alt="" src="/images/2011-05-31/2011-5-manage_feeds.png" width="650" height="434" /></p>

<p>Every feed can be uniquely composed from different NuGet packages from different sources: you can pull packages from the official <a href="http://www.nuget.org" target="_blank">NuGet Gallery</a>, you can upload packages, and in the future (we hope) you will be able to create packages from within the site as well!</p>

<p><img alt="" src="/images/2011-05-31/2011-5-manage_packages.png" width="650" height="518" /></p>

<p>If you want, you can also check out <a href="https://blog.maartenballiauw.be/post/2011/05/31/creating-your-own-private-nuget-feed-myget.html" target="_blank">Chuck Norris' feed on Maarten's blog</a> to get you started immediately.</p>

<p>Here are some wild ideas/suggestions we are thinking about:</p>

<ul>
<li>Create a My Account page (we currently support authentication through Live ID)</li>
<li>Support feed sharing management with other accounts (admins, readers, contributers)</li>
<li>Support ACS configuration for admin accounts to point e.g. to company ADFS for feed authentication</li>
<li><a href="http://myget.codeplex.com/workitem/list/basic" target="_blank">Many more on CodePlex</a> (do not hesitate to suggest some there)</li>
</ul>

<p>We believe the future is bright in the cloud, but please, let us know what *you *think!</p>

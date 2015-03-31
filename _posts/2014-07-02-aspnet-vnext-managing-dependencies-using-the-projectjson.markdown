---
layout: post
title: "ASP.NET vNext: Managing dependencies using the project.json file"
date: 2014-07-02 10:26:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET"]
alias: ["/post/ASPNET-vNext-Managing-dependencies-using-the-projectjson", "/post/aspnet-vnext-managing-dependencies-using-the-projectjson"]
---
<!-- more -->
{% include imported_disclaimer.html %}
<p><span style="background-color: #f5f5f5;">In ASP.NET vNext your dependencies, configurations aren&rsquo;t configured in the .csproj file like before. They added a project.json file where all this is done. This file is a simple JSON file that is much more readable than the .csproj file.</span></p>
<h2>Adding Dependencies</h2>
<p>Dependencies are defined using a name and version.</p>
<p><a href="/images/image_5.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_5.png" alt="image" width="542" height="266" border="0" /></a></p>
<p>And Visual Studio provides full intellisense to add dependencies.</p>
<p><a href="/images/image_6.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_6.png" alt="image" width="664" height="369" border="0" /></a></p>
<p><a href="/images/image_7.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_7.png" alt="image" width="662" height="240" border="0" /></a></p>
<p>You can use wildcard to get latest version of a specific set.</p>
<p><a href="/images/image_8.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_8.png" alt="image" width="648" height="296" border="0" /></a></p>
<p>If you always want the latest version, you&rsquo;ll need to specify an empty string.</p>
<p><a href="/images/image_9.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_9.png" alt="image" width="650" height="297" border="0" /></a></p>
<h1>Resolving dependencies in runtime</h1>
<p>When the runtime need to resolve a dependency, there is a chain of loaders the runtime uses to decide what it needs to load. Their will be a loader that will look for a Nuget Package, another will look for a project and another will look for assemblies on the disk. The main goal was to make Nuget packages the primary goal of reference. If you add Nuget Packages as a reference, ASP.NET will fetch those packages when you first deploy and run your application.</p>

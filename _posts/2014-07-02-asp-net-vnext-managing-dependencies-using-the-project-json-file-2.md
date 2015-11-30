---
ID: 46
post_title: 'ASP.NET vNext: Managing dependencies using the project.json file'
author: kennytordeur
post_date: 2014-07-02 08:26:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2014/07/02/asp-net-vnext-managing-dependencies-using-the-project-json-file-2/
published: true
---
In ASP.NET vNext your dependencies, configurations aren&#39;t configured in the .csproj file like before. They added a project.json file where all this is done. This file is a simple JSON file that is much more readable than the .csproj file.

<h2>Adding Dependencies</h2>

Dependencies are defined using a name and version.

<img src="http://blog.kennytordeur.be/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_5.png" alt="image">

And Visual Studio provides full intellisense to add dependencies.

<img src="http://blog.kennytordeur.be/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_6.png" alt="image">

<img src="http://blog.kennytordeur.be/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_7.png" alt="image">

You can use wildcard to get latest version of a specific set.

<img src="http://blog.kennytordeur.be/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_8.png" alt="image">

If you always want the latest version, you&#39;ll need to specify an empty string.

<img src="http://blog.kennytordeur.be/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_9.png" alt="image">

<h2>Resolving dependencies in runtime</h2>

When the runtime need to resolve a dependency, there is a chain of loaders the runtime uses to decide what it needs to load. Their will be a loader that will look for a Nuget Package, another will look for a project and another will look for assemblies on the disk. The main goal was to make Nuget packages the primary goal of reference. If you add Nuget Packages as a reference, ASP.NET will fetch those packages when you first deploy and run your application.
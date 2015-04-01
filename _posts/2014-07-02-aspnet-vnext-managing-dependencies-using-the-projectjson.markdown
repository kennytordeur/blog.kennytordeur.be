---
layout: post
title: "ASP.NET vNext: Managing dependencies using the project.json file"
date: 2014-07-02 10:26:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET"]
redirect_from: "/post/ASPNET-vNext-Managing-dependencies-using-the-projectjson"
alias: ["/post/ASPNET-vNext-Managing-dependencies-using-the-projectjson", "/post/aspnet-vnext-managing-dependencies-using-the-projectjson"]
---
In ASP.NET vNext your dependencies, configurations aren't configured in the .csproj file like before. They added a project.json file where all this is done. This file is a simple JSON file that is much more readable than the .csproj file.

##Adding Dependencies
Dependencies are defined using a name and version.

![image](/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_5.png)

And Visual Studio provides full intellisense to add dependencies.

![image](/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_6.png)

![image](/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_7.png)

You can use wildcard to get latest version of a specific set.

![image](/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_8.png)

If you always want the latest version, you'll need to specify an empty string.

![image](/images/2014-07-02-aspnet-vnext-managing-dependencies-using-the-projectjson/image_9.png)

##Resolving dependencies in runtime

When the runtime need to resolve a dependency, there is a chain of loaders the runtime uses to decide what it needs to load. Their will be a loader that will look for a Nuget Package, another will look for a project and another will look for assemblies on the disk. The main goal was to make Nuget packages the primary goal of reference. If you add Nuget Packages as a reference, ASP.NET will fetch those packages when you first deploy and run your application.

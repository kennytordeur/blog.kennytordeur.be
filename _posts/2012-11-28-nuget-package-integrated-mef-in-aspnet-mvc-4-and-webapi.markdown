---
layout: post
title: "Nuget package: integrated MEF in Asp.Net MVC 4 and WebApi"
date: 2012-11-28 13:26:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MEF", "MVC 4", "Nuget", "WebApi"]
alias: ["/post/Nuget-package-integrated-MEF-in-AspNet-MVC-4-and-WebApi", "/post/nuget-package-integrated-mef-in-aspnet-mvc-4-and-webapi"]
---
<!-- more -->

{% include imported_disclaimer.html %}

<p>Today i wanted to create my first Nuget package and i already knew what i would publish to the world. I wanted to make the code that is used to use MEF in Asp.Net MVC 4 and the WebApi ([view this post]({{ site.url }}/2012/08/31/mef-in-aspnet-mvc-4-and-webapi)) easy to reuse in other projects. I rewrote the code in a class libary. <br />After a little google-ing i found a <a href="http://www.youtube.com/watch?v=t7pYtAjkWUY" target="_blank">video</a> that explained how to create a Nuget package using the Nuget Package Explorer.&nbsp; After that i created a user at the <a href="http://nuget.org/" target="_blank">Nuget gallery</a> so i got my API Key. With this, i had all the information i needed to published my first Nuget package and i have a feeling that it will not be my last :-)<br />The <a href="https://nuget.org/packages/MEF.MVC4" target="_blank">package</a> can be installed from the Package Manager Console by typing: Install-Package MEF.MVC4</p>

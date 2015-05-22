---
layout: post
author: Kenny Tordeur
title: "Injecting a service in a view"
date: 2015-05-11 09:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET","MVC"]
alias: ["/post/injecting-a-service-in-a-view.markdown"]
---
In the ASP.NET MVC 6, you can now use the *@inject* keyword. This allows you to inject a service (class) into your view. This class must be public, non-nested and non-abstract.

The *@inject* needs 2 "parameters" in order to work. 

**@inject _[name of your class] [variable name that can be used in your view]_**


##My Qoutes service
This is the class that i want to inject into my view.

```csharp
using System.Collections.Generic;
using System;

namespace VSCode1.Models
{
    public class Quotes
	{
		List<string> _quotes = new List<string> { 
			"Don't cry because it's over, smile because it happened",
			"Be yourself; everyone else is already taken.",
			"Two things are infinite: the universe and human stupidity; and I'm not sure about the universe."	 
		 };

		public string GetRandomQuote()
		{
			Random r = new Random();				
			return _quotes[r.Next(0, _quotes.Count - 1)];
		}
			
		public List<string> GetQuotes()
		{
			return _quotes;
		}
	}
}
```
## Putting it in my view
To use the service in my view, i add the inject statement on top of the view. "quotes" can be used a variable in this view and is of type "VSCode1.Models.Quotes".

```html
@inject VSCode1.Models.Quotes quotes

<h1>@quotes.GetRandomQuote()</h1>

@foreach(var quote in quotes.GetQuotes())
{
    <h2>@quote</h2>
}
```
##Registering your service in your application
If i run my application, i would get an error saying that there is no service for type VSCode1.Models.Quotes.

![error](http://blog.kennytordeur.be/images/2015-05-22-Injecting-a-service-in-a-view/error.png)

When you use *@inject*, the runtime will use dependency injection to inject the type you requested. So your type needs to be registered in the dependency injector. This needs to be done in the *startup.cs* file.

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            //other stuff ...
            
            services.AddTransient<VSCode1.Models.Quotes>();
        }
```
Now the View renders without error.

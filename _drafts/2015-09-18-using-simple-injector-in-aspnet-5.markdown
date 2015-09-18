---
layout: post
author: Kenny Tordeur
title: "Using Simple Injector in Asp.Net 5"
date: 2015-09-18 09:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET","Dependency Injection"]
alias: ["/post/using-simple-injector-in-aspnet-5.markdown"]
---

In Asp.Net 5 it is really easy to do depedency injection. By default all the infrastructure is provided for you and you only need to configure your service container in the startup class. This is done in the ConfigureServices method.

```csharp
  public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddTransient(typeof(Repositories.IPersonRepository), typeof(Repositories.PersonRepository));            
        }
    }
```

Now the Asp.Net runtime will automatically inject an IPersonRespository in the constructor of my controller. 

```csharp
 public class HomeController : Controller
    {
        private readonly IPersonRepository _personRepository;
        
        public HomeController(IPersonRepository personRepository)
        {
            _personRepository = personRepository;            
        }
    }
```
But what if you want to use an other dependency injector?

##Using Simple Injector as a dependency injector

So i want to use Simple Injector as dependecy injector. To do this, you need to know that everything resolves rond the IServiceProvider interface. This interface is used to resolve types in Asp.Net. Luckily the container a Simple injector already implements this interface. If you dependency injection system doesn't implement this interface, you'll need to write a wrapper class that will implement this interface.

It's actually really easy to plug in your custom IOC. You only need to change the signature of the ConfigureServices in the Startup class. This method will now need to return an IServiceProvider.

```csharp
 public class Startup
    {
        public IServiceProvider ConfigureServices(IServiceCollection services)
        {
            // Add MVC services to the services container.
            services.AddMvc();
            
            var container = new SimpleInjector.Container();
            container.Register(typeof(Repositories.IPersonRepository), typeof(Repositories.PersonRepository));

            return container;            
        }
```

But this will not work at the moment. You will get an errors that certain services aren't registered. This is actually very logic, because Asp.Net will use the Simple Injector container to resolve all its services. All these services are defined in the Servicescollection we get as a parameter. So we will need to write something that will import the ServiceCollection in the Simple Injector container.



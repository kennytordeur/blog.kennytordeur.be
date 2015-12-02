---
layout: post
author: Kenny Tordeur
title: "Taghelpers and Dependency Injection"
date: 2015-10-15 09:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["asp.net","taghelper","mvc"]
description: "In the post I'll show you how easy it is to use dependency injection in an Asp.net mvc taghelper."
---

I was wondering if you could use Dependency Injection for taghelpers. I'll modify the example used in this my previous post [Creating a custom taghelper]({{site.baseurl}}/post/2015/10/14/creating-a-custom-taghelper).

## Creating a service to inject
```csharp
    public interface IPersonService
    {
        string GetName();
    }

    public class PersonService : IPersonService
    {
        public String GetName()
        {
            return "PersonService.GetName()";
        }
    }
```

Register this class in the DI-system of Asp.net. Add it to services collection in the ConfigureServices method from the Startup class.

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
        services.AddTransient<IPersonService, PersonService>();
  }
```

## Modifiying the taghelper
```csharp
    [TargetElement("Person")]
    public class MyTagHelper : TagHelper
    {
        IPersonService _personService;

        public MyTagHelper(IPersonService personService)
        {
            _personService = personService;
        }

        public override void Process(TagHelperContext context, TagHelperOutput output)
        {
            output.Content.SetContent(_personService.GetName());

            base.Process(context, output);
        }
    }
```

##Testing
When I run the application, the DI-system will also inject dependencies for taghelpers.

![The result]({{site.baseurl}}/images/2015-10-15-Taghelpers and DI/result.png)




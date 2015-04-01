---
layout: post
title: "ASP.NET MVC and Unity"
date: 2011-04-23 09:40:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC", "Unity"]
alias: ["/post/ASPNET-MVC-and-Unity", "/post/aspnet-mvc-and-unity"]
redirect_from: "/post/ASPNET-MVC-and-Unity"
---
##What is Unity?

The Unity Application Block (Unity) is a lightweight extensible dependency injection container with support for constructor, property, and method call injection. Dependency injection is used to create loosely coupled applications. These kind of applications are easier to maintain and easier to test.

##What do we want to achieve?##

We want to make our controllers loosely coupled and break any hard dependencies it has. Every object/resource that our controller needs, has to be automatically populated and correctly initiated by Unity.


##The Asp.net mvc application##

This is the HomeController that we have in our application.

```csharp
public class HomeController : Controller
{
    ITitleRepository _titleRepository = null;

    public HomeController()
    {
        _titleRepository = new TitleRepository();
    }
    
    //
    // GET: /Home/
    public ActionResult Index()
    {
        ViewBag.Title = _titleRepository.GetTitle("Index");

        return View();
    }
}
```

As you can see, it uses a TitleRepository. This repository is used to determine which title has to be rendered on a specific page.

```csharp
titleRepository = new TitleRepository();
```

This is the line that we want to remove. With this, we make a hard dependency between our HomeController and the TitleRepository.

```csharp
    public interface ITitleRepository
    {
        string GetTitle(String page);
    }
 
    public class TitleRepository : MVCWebsite.Repository.ITitleRepository
    {
        public String GetTitle(String page)
        {
            return String.Format(" This is a Title for the page: {0}", page);
        }
    }
```

##Using Unity##
###Adding references

Add the Microsoft.Practices.Unity dll to you project. Unity can be found at http://unity.codeplex.com/.

###Creating a custom LifetimeManager

When you register a type with Unity, you can also specify a lifetime of this type. If you don't specify a lifetime, Unity will create a new instance of that type each time that the type is requested. In an Asp.Net application, we can make the LifetimeManager cache or store the created objects in the HttpContext HttpSession or HttpApplication. In my case, i want a singleton per request.

```csharp
    public class HttpContextLifetimeManager<T> : LifetimeManager
    {
        public override void SetValue(object newValue)
        {
            HttpContext.Current.Items[typeof(T).AssemblyQualifiedName] = newValue;
        }

        public override object GetValue()
        {
            return HttpContext.Current.Items[typeof(T).AssemblyQualifiedName];
        }

        public override void RemoveValue()
        {
            HttpContext.Current.Items.Remove(typeof(T).AssemblyQualifiedName);
        }  
    }
```


###Creating the UnityContainer

Next in line is the creation of a UnityContainer. This container will be used to resolve certain types. In order to work, we have to register these types. In our case, we our going to register the ITitleRepository type with the TitleRepository type. This will mean that if we ask the container to resolve the ITitleRepository, it will return a TitleRepository object. As you see, i pass the HttpContextLifetimeManager as parameter.

```csharp
public class UnityConfigurator
{
    public static IUnityContainer GetContainer()
    {
        IUnityContainer container = new UnityContainer();

        container.RegisterType<ITitleRepository, TitleRepository>(new HttpContextLifetimeManager<ITitleRepository>());

        return container;
    }
}
```

Now we are registering our type hardcoded. It is also possible doing this in the config file.

###Creating a Controller Factory

We need to create a custom Controller factory to replace the default factory that is used by the MVC framework. Our factory accepts a IUnityContainer as parameter. This container contains all the types we registered.

We only need to override the GetControllerInstance method. In this method we have to use the container to create an instance of the requested controller. This way, Unity will automatically resolve the requested types for this controller. In our case: ITitleRepository.

```csharp
    public class UnityControllerFactory : DefaultControllerFactory
    {
        IUnityContainer container = null;

        public UnityControllerFactory(IUnityContainer container)
        {
            this.container = container;
        }

        protected override IController GetControllerInstance(System.Web.Routing.RequestContext requestContext, Type controllerType)
        {
            IController result = null;

            if (null != controllerType)
            {
                result = container.Resolve(controllerType) as IController;
            }

            return result;
        }
    }
```

Now we have to tell the MVC framework to use the UnityControllerFactory as it's default controller factory. This is done best in the global.asax, in the Application_Start method.

```csharp
protected void ApplicationStart()
{
    AreaRegistration.RegisterAllAreas();

    RegisterGlobalFilters(GlobalFilters.Filters);
    RegisterRoutes(RouteTable.Routes);
    ControllerBuilder.Current.SetControllerFactory(new UnityControllerFactory(UnityConfigurator.GetContainer()));
}
```

##Breaking the dependency

Now we have to modify our HomeController. We change it's constructor to accept an object of the ITitleRepository type.

```csharp
    public class HomeController : Controller
    {
        ITitleRepository _titleRepository = null;

        public HomeController(ITitleRepository titleRepository)
        {
            titleRepository = titleRepository;
        }

        //
        // GET: /Home/
        public ActionResult Index()
        {
            ViewBag.Title = titleRepository.GetTitle("Index");

            return View();
        }
    }
```


##The result

When we go to the index of the HomeController, our UnityControllerFactory comes in action and Untity will inject the TitleRepository in the constructor of the HomeController.

When i want to use another implementation of the ITitleRepository, i only have to change the GetContainer method of the UnityConfigurator and the HomeController will automatically be injected with the new implementation.

```csharp
public class TitleRepository2 : MVCWebsite.Repository.ITitleRepository
{
    public string GetTitle(String page)
    {
        return String.Format("{0} is the Title for this page!", page.ToUpper());
    }
}

public class UnityConfigurator
{
    public static IUnityContainer GetContainer()
    {
        IUnityContainer container = new UnityContainer();

        container.RegisterType<ITitleRepository, TitleRepository2>(new HttpContextLifetimeManager<ITitleRepository>());

        return container;
    }
}
```

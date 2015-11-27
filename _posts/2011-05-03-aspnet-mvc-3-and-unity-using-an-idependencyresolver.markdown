---
layout: post
title: "ASP.NET MVC 3 and Unity using an IDependencyResolver"
date: 2011-05-03 16:08:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC", "Unity"]
alias: ["/post/ASPNET-MVC-3-and-Unity-using-an-IDependencyResolver", "/post/aspnet-mvc-3-and-unity-using-an-idependencyresolver"]
redirect_from: "/post/ASPNET-MVC-3-and-Unity-using-an-IDependencyResolver"
---
t
In this [post]({{site.url}}/post/2011/04/23/aspnet-mvc-and-unity) i used a custom ControllerFactory for dependency injection. I created a custom ControllerFactory where i overrided the GetControllerInstance method. In this method i used the UnityContainer for creating instances of the requested controller.

In ASP.NET MVC 3 their is some build in abstraction for doing dependency injection ( [IDependencyResolver](http://msdn.microsoft.com/en-us/library/system.web.mvc.idependencyresolver.aspx)). I will change the example used in this [post]({{site.url}}/post/2011/04/aspnet-mvc-and-unity) to use the IDependencyResolver.

##the IDependencyResolver

The IDependencyResolver is a fairly easy interface. It contains only 2 methods:</p>
<ul>
* object GetService (Type serviceType)
  * The GetService method is used to resolve a single registered service
* IEnumerable<object> GetServices(Type serviceType)
  * The GetServices method resolves all registered services

In our case, we will create an UnityDependencyResolver. This implementation will accept an IUnityContainer and will use this container for resolving the requested types. If the DependencyResolver is unable to resolve a type, it should always return null for the GetService method and an empty collection for the GetServices method. That is why a try catch is used in the UnityDependencyResolver.

```csharp
    public class UnityDependencyResolver : IDependencyResolver
    {
        IUnityContainer container = null;

        public UnityDependencyResolver(IUnityContainer container)
        {
            this.container = container;
        }

        #region IDependencyResolver Members

        public object GetService(Type serviceType)
        {
            try
            {
                return container.Resolve(serviceType);
            }
            catch
            {
                return null;
            }
        }

        public IEnumerable<object> GetServices(Type serviceType)
        {
            try
            {
                return container.ResolveAll(serviceType);
            }
            catch
            {
                return new List<object>();
            }
        }

        #endregion
    }
```

##Registering the UnityDependencyResolver

Now that we created this dependency resolver, we have to register it so that the MVC framework will use it. This is done in the Global.asax, in the Application_Start method.

```csharp
  protected void ApplicationStart()
  {
      AreaRegistration.RegisterAllAreas();

      RegisterGlobalFilters(GlobalFilters.Filters);
      RegisterRoutes(RouteTable.Routes);
      
      DependencyResolver.SetResolver(new UnityDependencyResolver(UnityConfigurator.GetContainer()));
  }
  ```
        

You could also register it inside a WebActivator.PreApplicationStartMethod.

##Changing the UnityControllerFactory

In the UnityControllerFactory we now can use the IDependencyResolver that we registered to resolve the requested controllers. It doesn't need to work with an IUnityContainer any more.

```csharp
    public class UnityControllerFactory : DefaultControllerFactory
    {        
        protected override IController GetControllerInstance(System.Web.Routing.RequestContext requestContext, Type controllerType)
        {
            IController result = null;

            if (null != controllerType)
            {
                result = DependencyResolver.Current.GetService(controllerType) as IController;
            }

            return result;
        }
    }
```

##Changing the UnityConfigurator

As you might have noticed the Application_Start method of the Global.asax has changed a bit. The line for registering our UnityControllerFactory has been removed (see [this](http://blog.kennytordeur.be/post/2011/04/aspnet-mvc-and-unity.html). The MVC 3 framework will use it's current DependencyResolver to resolve a ControllerFactory, so we have to register our UnityControllerFactory with the IUnityContainer. Now the MVC 3 framework will use our UnityControllerFactory as it's default ControllerFactory.

```csharp
public class UnityConfigurator
{
    public static IUnityContainer GetContainer()
    {
        IUnityContainer container = new UnityContainer();

        container.RegisterType<IControllerFactory, UnityControllerFactory>();
        container.RegisterType<ITitleRepository, TitleRepository2>(new HttpContextLifetimeManager<ITitleRepository>());

        return container;
    }
}
```

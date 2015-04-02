---
layout: post
title: "Asp.net MVC MEF: Per Request Lifetime"
date: 2012-12-21 08:52:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["MEF", "MVC 4"]
redirect_from: "/post/Aspnet-MVC-MEF-Per-Request-Lifetime"
alias: ["/post/Aspnet-MVC-MEF-Per-Request-Lifetime", "/post/aspnet-mvc-mef-per-request-lifetime"]
---
Like the title says, i want to use MEF in an Asp.Net MVC application and the lifetime of the object has to be exactly the same as the lifetime of the request. In short, i want 1 single unique object that is recreated per request.


In MEF there is a way to control the lifetime of the created object. This is specified the PartCreationPolicy attribute that you can use to decorate the object. In my test project i installed the nuget package i created in this [post](http://blog.kennytordeur.be/post/2012/11/nuget-package-integrated-mef-in-aspnet) to incorporate MEF in an Asp.net MVC application. I also created an object called MyCustomObject with an interface IMyCustomObject. This object has only one property called Created that contains the date when the object is created. In the constructor of that object i have but an thread.sleep. This way i am sure that the Created property will be different for every created object.

```csharp
  public interface IMyCustomObject
  {
      DateTime Created { get; set; }
  }

  [Export(typeof(IMyCustomObject))]    
  public class MyCustomObject : IMyCustomObject
  {
       public DateTime Created { get; set; }

       public MyCustomObject()
       {
           Created = DateTime.Now;
           System.Threading.Thread.Sleep(1000);
       }
  }
```

I also created an object called MyCustomObjectContainer. This object will contain a property of the type IMyCustomObject.

```csharp
 [Export]   
 public class MyCustomObjectContainer
 {
     [Import]
     public IMyCustomObject MyCustomObject { get; set; }
 }
```

The controller will have 2 properties. The first of type IMyCustomObject aand the other of type MyCustomContainer. In the action method of the controller, i will write the Created value that is found in those 2 properties to the viewbag. That way i will be sure if the application is using the same object.

```csharp
  [Export]
  [PartCreationPolicy( CreationPolicy.NonShared)]
  public class HomeController : Controller
  {
      [Import]
      public IMyCustomObject MyCustomObject { get; set; }

      [Import]
      public MyCustomObjectContainer MyCustomObjectContainer { get; set; }

      public ActionResult Index()
      {
          this.ViewBag.MyCustomtObjectDateCreated = MyCustomObject.Created;
          this.ViewBag.MyCustomObjectContainerDateCreated = MyCustomObjectContainer.MyCustomObject.Created;
          return View();
      }       
  }
```

##Choosing the right PartCreationPolicy


In MEF there are 3 different type of creation policies that we can choose. More information can be found [here](http://mef.codeplex.com/wikipage?title=Parts%20Lifetime).

* Shared: the part author is telling MEF that at most one instance of the part may exist per container.
* NonShared: the part author is telling MEF that each request for exports of the part will be served by a new instance of it.
* Any or not supplied value: the part author allows the part to be used as either "Shared" or "NonShared".

###The Shared Creation policy</h2>

We will set Creation policy to Shared and run the application.

![image](http://blog.kennytordeur.be/images/2012-12-21-aspnet-mvc-mef-per-request-lifetime/image1.png)

We see that the same instance of the MyCustomOject is used. But when we refresh the page, we will get exactly the same value, over and over again. So this is not the behavior that we want.


###The NonShared Creation Policy

We will set the creation policy to NonShared.

![image](http://blog.kennytordeur.be/images/2012-12-21-aspnet-mvc-mef-per-request-lifetime/image2.png)

With every refresh, we will get a different value for the Created property, but everytime that an IMyCustomObject is asked form the MEF container, a new value is created so this is not a solution for my problem.

##The solution

We can create a solution for this problem using the Proxy-pattern, HttpContext and ExportFactory<T>.

The ExportFactory<T> class was introduced in MEF 2. It allows us to create new instances and control the lifetime of those instances. This will be perfect to create the MyCustomObject object since we need to control the lifetime of this object.

The HttpContext contains Http information about an HttpRequest. There is one property that is very interesting in our case, the Items property. This property is in fact a dictionary where you can insert objects into. This collection is cleared with every request (perfect). We will use this dictionary to add the created MyCustomObject. So every time that this object is requested, we will look in this dictionary. When it is not found, the ExportFactory<T> will create it and add it to the dictionary.

The proxy pattern will be used to make this a transparent as possible.

![image](http://blog.kennytordeur.be/images/2012-12-21-aspnet-mvc-mef-per-request-lifetime/image3.png)

###The Implementation

I'll create an IItemContainer that has one property Container of type IDictionary. Then i'll create an HttpContextContainer that will implement this interface. This object will map the Container property to the HttpContext.Items property.

```csharp
public interface IItemContainer
{
  IDictionary Container { get; }
}

[Export(typeof(IItemContainer))]
[PartCreationPolicy(CreationPolicy.Shared)]
public class HttpContextContainer : MEF_Asp.Net_MVC_Per_Request.Models.IItemContainer
{
    public IDictionary Container
    {
        get
        {
          return HttpContext.Current.Items;
        }
    }       
}
```

Next I'll create the proxy object (MyCustomObjectPerRequest). This object will contain the IItemContainer and the Exportfactory<T> instances and implement the IMyCustomObject. This object will be served by the MefContainer when something is request of type IMyCustomObject.

```csharp
    [Export(typeof(IMyCustomObject))]
    public class MyCustomObjectPerRequest : IMyCustomObject
    {
        [Import]
        public ExportFactory<MyCustomObject> Factory { get; set; }

        [Import]
        public IItemContainer ItemContainer { get; set; }

        private IMyCustomObject MyCustomObject
        {
            get
            {
                if (null == ItemContainer.Container["MyCustomObject"])
                {
                    ItemContainer.Container["MyCustomObject"] = Factory.CreateExport().Value;
                }

                return (IMyCustomObject)ItemContainer.Container["MyCustomObject"];
            }
        }

        public DateTime Created
        {
            get
            {
                return MyCustomObject.Created;
            }
            set
            {
                MyCustomObject.Created = value;
            }
        }
    }
```

We also need to change the ExportAttribute on the MyCustomObject class so that it doesn't specify a ContractType.</p>
```csharp
    [Export] 
    public class MyCustomObject : IMyCustomObject
```

###The Result

When we know run the application, we will use the same MyCustomObject object per request. When we refresh, we'll get an other instance.

![image](http://blog.kennytordeur.be/images/2012-12-21-aspnet-mvc-mef-per-request-lifetime/image4.png)

##Conclusion

This solution can also be used to use the same instance per Session or to incorporate a caching mechanism.&nbsp; All that needs to been done is to implement the IItemsContainer and map the Container property. The Visual Studio solution can be downloaded [here](http://dl.dropbox.com/u/41091233/Blog/MEF%20Asp.Net%20MVC%20Per%20Request/MEF%20Asp.Net%20MVC%20Per%20Request.rar).

---
layout: post
title: "ASP.NET VNext: Routing constraints and custom constraints"
date: 2014-07-11 15:37:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET"]
redirect_from: "/post/aspnet-vnext-routing-constraints-and-custom-constraints"
alias: ["/post/ASPNET-VNext-Routing-constraints-and-custom-constraints", "/post/aspnet-vnext-routing-constraints-and-custom-constraints"]
---
In ASP.NET VNext the routing system has been unified and it is build as a middleware component that easily can be plugged in. This routing system can be found in the “Microsoft.AspNet.Routing” Nuget Package or add [github](https://github.com/aspnet/Routing) if you want to work with the source code.

##Constraints
Here is an example how we putted constraints on our route parameters in MVC 5. In this case the parameter “productId” must match an integer.

```csharp
routes.MapRoute(
    "Product", 
    "Product/{productId}", 
    defaults: new { controller = "Product", action = "Details" }, 
    constraints: new { productId = @"\d+" });
```

ASP.NET VNext introduced a new syntax for defining constraints:</p>

<span style="color: #c0504d; font-size: medium">“{parameter:constraint}”</span>

<span style="color: #c0504d; font-size: 12pt">parameter </span>will contain the name of the parameter you want to constraint and <span style="color: #c0504d; font-size: 12pt">constraint </span>will specify with constraint will be applied.

![constraints](http://blog.kennytordeur.be/images/2014-07-11-aspnet-vnext-routing-constraints-and-custom-constraints/constraints.png)

For a full overview of the available constraints you can take a look at [Microsoft.AspNet.Routing/Constraints](htpps://github.com/aspnet/Routing/tree/dev/src/Microsoft.AspNet.Routing/Constraints).

So the previous constraint will look like this in ASP.NET VNext.

```csharp
routes.MapRoute(
    "Product", 
    "Product/{productId:int}", 
    new { controller = "Product", action = "Details" });
```
You also can make the parameter “nullable” using <span style="color: #c0504d">“{parameter:constraint?}” </span>syntax.

```csharp
routes.MapRoute(
    "Product", 
    "Product/{productId:int?}", 
    new { controller = "Product", action = "Details" });
```

Or pass in a default value <span style="color: #c0504d">“{parameter:constraint=value}”.

```csharp
routes.MapRoute(
    "Product", 
    "Product/{productId:int=1}", 
    new { controller = "Product", action = "Details" });
```

##Adding your custom constraints

If the buildin constraints don’t meet your needs, you can also create a custom constraint based on the IRouteConstraint interface.

```csharp
public class AreYouLuckyRouteConstraint : IRouteConstraint
{
	public bool Match(HttpContext httpContext, IRouter route, string routeKey, IDictionary<string, object> values, RouteDirection routeDirection)
        {
            object value;
            if (values.TryGetValue(routeKey, out value) && value != null)
            {
                int result;
                var valueString = Convert.ToString(value, CultureInfo.InvariantCulture);
                var isInt =  Int32.TryParse(valueString, NumberStyles.Integer, CultureInfo.InvariantCulture, out result);


            if(isInt)
            {
                Random rand = new Random(10);
                return result == rand.Next(100);
            }
            else
            {
               return false;
            }
        }
        return false;
    }
}
```


This route constraint will test how lucky you are and will only pass when you passed a correct integer value between the number 0 and 100. Now we need to add our custom constraint to the application route options in the Startup class.

```csharp
	var routeOptions = app.ApplicationServices.GetService<IOptionsAccessor<RouteOptions>>();
	routeOptions.Options.ConstraintMap.Add("areyoulucky", typeof(AreYouLuckyRouteConstraint));
```
Now you can use your custom constraint.

```csharp
routes.MapRoute(
    "Product", 
    "Product/{productId:areyoulucky}", 
    new { controller = "Product", action = "Details" });
```

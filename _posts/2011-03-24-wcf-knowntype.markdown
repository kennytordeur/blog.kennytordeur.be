---
layout: post
title: "WCF KnownType"
date: 2011-03-24 06:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["WCF"]
alias: ["/post/WCF-KnownType", "/post/wcf-knowntype"]
redirect_from: "/post/WCF-KnownType"
---

In object-oriented programming the concept of polymorphism is frequently used. This can cause problems in a WCF service if no action is taken. This is where **KnownType** comes to the rescue.

This is the WCF service that we are going to use in this example.

```csharp
    [ServiceContract(
        Namespace="http://localhost/WeatherForeCast"
        ,Name="WeatherForeCastService")]
    public interface IWeatherForeCastService
    {
        [OperationContract]
        WeatherForeCast GetWeatherForeCast(DateTime day);
    }
	public class WeatherForeCastService : IWeatherForeCastService
    {
        public WeatherForeCast GetWeatherForeCast(DateTime day)
        {
            return new WeatherForeCast { Celsius = 30, Day = day };
        }
    }
```

This service has only 1 method. The GetWeatherForeCast method gives us a weather forecast for a specific day ( = parameter ).

This is how the WeatherForeCast object looks like.

```csharp
    [DataContract]
    public class WeatherForeCast
    {
        [DataMember]
        public int Celsius { get; set; }

        [DataMember]
        public DateTime Day { get; set; }
    }
```

Fore some reason, the service method must also return Farenheit as property on all the uneven days. So we decide to create a new class WeatherForeCastExtra that inherits from WeatherForeCast and add the extra property.


```csharp
    [DataContract]
    public class WeatherForeCastExtra: WeatherForeCast
    {        
        [DataMember]
        public int Farenheit { get; set; }
    }
```

Off course we also have to modify our service so that it could return a WeatherForeCastExtra object on all the uneven days.
 
```csharp
    public class WeatherForeCastService : IWeatherForeCastService
    {
        public WeatherForeCast GetWeatherForeCast(DateTime day)
        {
            if ((day.Day % 2) == 0)
            {
                return new WeatherForeCast { Celsius = 30, Day = day };
            }
            else
            { 
                return new WeatherForeCastExtra { Celsius = 30, Farenheit = 86, Day=day};
            }
        }
    }
```

If we now create a client for this and ask for a forecast on a even day, it will work fine. If you ask for a forecast on an uneven day, you will get an error.

```csharp
    namespace WeatherForeCastClient
	{
    	class Program
    	{
    	    static void Main(string[] args)
    	    {
    	        using (WeatherForeCastService.WeatherForeCastServiceClient client = new			WeatherForeCastService.WeatherForeCastServiceClient())
            	{
                	WeatherForeCastService.WeatherForeCast weatherForeCast = client.GetWeatherForeCast(new DateTime(2011, 3, 24));
	                weatherForeCast = client.GetWeatherForeCast(new DateTime(2011, 3, 23));
	            }
	        }
	    }
	}
```

The problem is that the client doesn't have any idea how the WeatherForeCastExtra object looks like or how it has to be deserialized. If we look at the WSDL our service generates, we can't find a definition for the WeatherForeCastExtra type.

![wsdl](http://blog.kennytordeur.be/images/2011-03-24-wcf-knowntype/wsdl.png)

It's kind of logic that we can't find a definition for the WeatherForeCastExtra type. If we look at our service interface, we see that the service returns the WeatherForeCast type. We have to find some way to tell the client that the service also returns something off the WeatherForeCastExtra type. This is were KnownType comes into action.

The KnownType attribute lets you specify types that should be include during serialization/deserialization. We have to use this attribute in our base class ( WeatherForeCast ).

```csharp
    [KnownType(typeof(WeatherForeCastExtra))]
	[DataContract]
	public class WeatherForeCast
	{
	    [DataMember]
	    public int Celsius { get; set; }
	sdssdsds
	    [DataMember]
	    public DateTime Day { get; set; }
	}
```


If we now look at the generate WSDL file of our service, we will find a definition for our WeatherForeCastExtra type and the DataContractSerializer will know how the serialize or deserialize our WeatherForeCastExtra type.

![wdsl](http://blog.kennytordeur.be/images/2011-03-24-wcf-knowntype/wsdl2.png)

If we run our client now, we won't get an exception on uneven days.


You can imagine that, if you have to include 10 types, your class would be decorated with 10 KnownType attributes and would be a little hard to read. This is why the KnownType attribute has another constructor. This constructor accepts a method name, as a string, of a static method that returns an array of types. These types will be include in the WDSL definition of the service.

```csharp
    [KnownType("GetKnowTypes")]
	[DataContract]
	public class WeatherForeCast
	{
	    [DataMember]
	    public int Celsius { get; set; }
	
	    [DataMember]
	    public DateTime Day { get; set; }
	
	    static Type[] GetKnowTypes()
	    {
	        return new Type[] { typeof(WeatherForeCastExtra) };
	    }
	}
```
This will return the same WSDL as before and our client will still work.

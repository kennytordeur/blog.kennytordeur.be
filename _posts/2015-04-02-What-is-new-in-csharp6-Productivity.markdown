---
layout: post
author: Kenny Tordeur
title: "What's new in C# 6.0: Productivity"
date: 2015-04-02 09:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["C#"]
alias: ["/post/What-is-new-in-csharp6-Productivity", "/post/what-is-new-in-csharp6-productivity"]
---
Version 6 of C# contains a bunch of new feature that allows to speed up the productivity of the developer.

## Feature 1 : static usings

The classes are structured by their usings. If you need a specific class, you'll need to add a using containing the namespace of that class. If you do this, you'll get access to all the classes that are defined in that using. With static usings, you can bring only a specific class into "scope" which will you access to all of it's static methods.

Consider following console application.

```csharp
using System;

namespace ConsoleApplication8
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello world");
        }
    }
}
```

If i use a static using for the "Console" class, i can do this.

```csharp
using static System.Console;

namespace ConsoleApplication8
{
    class Program
    {
        static void Main(string[] args)
        {
            WriteLine("Hello world");
        }
    }
}
```

## Feature 2 : ? a.k.a the elvis operator

Consider following object graph.

```csharp
    public class ObjectA
    {
        public ObjectB ObjectB { get; set; }
    }

    public class ObjectB
    {
        public ObjectC ObjectC { get; set; }
    }

    public class ObjectC
    {
        public string Name { get; set; }
    }
```

If you want to access the "Name" property of "ObjectC" via ObjectA, i'll write code that looks like this.

```csharp
    public void SomeMethod(ObjectA objectA)
    {
        string name = null;

        if (null != objectA.ObjectB)
            if (null != objectA.ObjectB)
                if (null != objectA.ObjectB.ObjectC)
                    name = objectA.ObjectB.ObjectC.Name;
    }
```

I'll needs those if-statements because you don't know if the child object isn't null. With the elvis operator ? you can remove all the if-statements and make the code a little more readable.

```csharp
    public void SomeMethod(ObjectA objectA)
    {
        string name = objectA?.ObjectB?.ObjectC?.Name;
    }
```

If one of the child objects is null, we'll get the default value for a "string" object.

##Feature 3:  Expression Bodied Members

Expression Bodies Members give you the possibility to write short methods even shorter.

```csharp
    public class ObjectC
    {
        public string Name { get; set; }

        public override string ToString()
        {
            return Name;
        }
    }
```

The "ToString" method can be writing shorter using expressions.

```csharp
    public class ObjectC
    {
        public string Name { get; set; }

        public override string ToString() => Name;
    }
```

You can only do this for expression on a single line. If you would write it like this, it won't build and Visual Studio will give build errors.

```csharp
    public class ObjectC
    {
        public string Name { get; set; }

        public override string ToString() =>  
        {
            return Name;
        }
    }
```














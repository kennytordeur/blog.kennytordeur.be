---
layout: post
author: Kenny Tordeur
title: "Creating a custom taghelper"
date: 2015-10-14 09:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET","taghelper"]
alias: ["/post/2015-10-14-creating-a-custom-taghelper.markdown"]
---

In this post i'll show how you can create a custom taghelper.

##What are taghelpers?

Basically taghelpers do the same thing as the htmlhelpers we all know. If you use a htmlhelper in your view, you'll see that it is actually C# code. If you would use a taghelper, it would lean more to html than to C# code.

##Defining your taghelper
Your custom taghelper will need to inherit from the TagHelper base class. This base class defines two virtual methods called Process and ProcessAsync. In these methods you'll write your taghelper logic.

```csharp
    public class MyTagHelper : TagHelper
    {
        public override void Process(TagHelperContext context, TagHelperOutput output)
        {
            base.Process(context, output);
        }
    }
```

In order to use your taghelper, i'll need to register it. This done in the "_ViewImports.cshtml" file that can be found in the View folder. 

```csharp
      @addTagHelper "MyWebApplication.MyTagHelper, MyWebApplication"
```

The last part is your dll name. The first part is the name (namespace + classname) of the taghelper you want to register. If you want to register all the taghelpers from a specific dll you can use a wildcard.

```csharp
      @addTagHelper "*, MyWebApplication"
```

When you succesfully have registered your taghelper, i'll get intellisense for your taghelper in your views.

Next, you will need to specify a target html tag that will invoke your custom taghelper. This can be done in 2 ways:
	<ul>
		<li>by naming convention</li>
		<li>
			<ul>
				<li> If your your taghelper is called TestTagHelper, it will target <code><test></code></li>
			</ul>
		</li>
		<li>using the TargetElement attribute</li>
		<li>
			<ul>
				<li>you explicitly set the target tag</li>
			</ul>
		</li>
	</ul>
```csharp
    [TargetElement("Person")]
    public class MyTagHelper : TagHelper
    {        
        public override void Process(TagHelperContext context, TagHelperOutput output)
        {
            base.Process(context, output);
        }
    }
```
##Adding attributes/properties on a taghelper
I now want to pass a name and lastname to the custom taghelper. These parameters will be passed like this:

```html
<Person name="kenny" lastname="tordeur"></Person>
```

In the taghelper you will need to define these attributes. This can be done using the TargetElement tag.

```csharp
    [TargetElement("Person",Attributes = "name,lastname")]
    public class MyTagHelper : TagHelper
    {        
        public override void Process(TagHelperContext context, TagHelperOutput output)
        {
            base.Process(context, output);
        }
    }
```

In order to get the values of the attributes, you can use the TagHelperContext. This object will contain all the defined attributes. You can also create properties that will map to the attribute names.

```csharp
    [TargetElement("Person",Attributes = "name,lastname")]
    public class MyTagHelper : TagHelper
    {
        public string Name { get; set; }

        public string Lastname { get; set; }

        public override void Process(TagHelperContext context, TagHelperOutput output)
        {
            base.Process(context, output);
        }
    }
```
To map the attribute values to the correct properties, a naming convention is used. The attribute "name" will map to the property "Name". So the first letter of the attribute will be transformed to an uppercased letter. If you would have an attribute "last-name", it would be mapped to "LastName" property. So the "-" will be remove from the attribute name and the next letter will be transformed to an uppercased letter.

You aren't just limited by passing in simple types (string, int, ...) to the taghelper. You can also pass complex types to the taghelper.

##Rendering some html
The only thing left to do is to implement an output for the taghelper. This can be done using the TagHelperOutput object. This object contains everything you need to create your custom html output. In this example i'll just concatent the Name with the Lastname.

```csharp
    [TargetElement("Person",Attributes = "name,lastname")]
    public class MyTagHelper : TagHelper
    {
        public string Name { get; set; }

        public string Lastname { get; set; }

        public override void Process(TagHelperContext context, TagHelperOutput output)
        {
            output.Content.SetContent($"{Name}, {Lastname}");

            base.Process(context, output);
        }
    }
```

##The result
I've but this in my view.

```html
	<Person lastname="Tordeur" name="Kenny"></Person>
```

The intellisense helped me.

![intellisense](http://blog.kennytordeur.be/images/2015-10-14-creating-a-custom-taghelper/intellisense.png)

This is the output when the view is rendered in the browser.

```html
	<Person>Kenny, Tordeur</Person>
```


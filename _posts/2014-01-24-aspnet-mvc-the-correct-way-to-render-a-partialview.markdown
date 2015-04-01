---
layout: post
title: "Asp.net MVC: The correct way to render a partialview"
date: 2014-01-24 14:51:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC"]
redirect_from: "/post/Aspnet-MVC-The-correct-way-to-render-a-partialview"
alias: ["/post/Aspnet-MVC-The-correct-way-to-render-a-partialview", "/post/aspnet-mvc-the-correct-way-to-render-a-partialview"]
---
When you create an Asp.net mvc application will create partial views in order to use them as "user controls" throughout your application. But you have to use them with caution.

These are my viewmodels.

```csharp
    public class ParentViewModel
    {
        public string Description { get; set; }

        public ChildViewModel Child { get; set; }

        public ParentViewModel()
        {
            Child = new ChildViewModel();
        }
    }

    public class ChildViewModel
    {
        public string Description { get; set; }
    }
```

The ChildViewModel will be used on other different views, so I'll create a partial view for it. So my index.cshtml will look like this.

![image](http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image.png)

But when I submit my form, I won't get a Description for my ChildViewModel on the controller site. This is because the partial view for my ChildViewModel doesn't render like it should. The generate name for the Description field is wrong.

![image](http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image_1.png)

The "Child" prefix isn't added to the Description field so the modelbinder doesn't know that this is a property of the ChildViewModel.
Now when I convert my partial view to an editor template and use the EditorFor method the prefix will be added automatically.

![image](http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image_2.png)

The EditorFor method will actually add a "TemplateInfo" key to the ViewData of the editor template. This key will contain an instance of System.Web.Mvc.TemplateInfo. This object contains a HtmlFieldPrefix property that will be used by the TextBoxFor methode in order to generate the correct name.


So if we want to use the Partial method we'll need to pass a ViewDataDictionary with a TemplateInfo key that will contain the correct HtmlFieldPrefix.

![image](http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image_4.png)

##Conclusion
If your partialview needs to post data to the controller, I would suggest to transform your partialview to an editor template and use the EditorFor method. This will generate the correct HtmlFieldPrefix for you. But if you need to show a list and you need unique names. I would iterate the list and pass in a custom TemplateInfo to the Partail method for each item in the list. If you only need to show data you can use partial method.

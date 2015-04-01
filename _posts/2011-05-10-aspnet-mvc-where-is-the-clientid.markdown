---
layout: post
title: "ASP.NET MVC: Where is the ClientID?"
date: 2011-05-10 16:36:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC"]
alias: ["/post/ASPNET-MVC-Where-is-the-ClientID", "/post/aspnet-mvc-where-is-the-clientid"]
redirect_from: "/post/ASPNET-MVC-Where-is-the-ClientID"
---
For those who are used to program in ASP.NET know this scenario. You have a textbox and you want to do some custom validation in Javascript. You have to be able to access the textbox from the Javascript. The first thing you need to know is the Id for that textbox. You can't hard code it, because you are not sure how ASP.NET will generate the Id. If the control exists in an INamingContainer, the INamingContainer will add some prefix to the Id of the control.


That is where the ClientID property comes to the rescue. This property contains the Id that will be used in HTML. Take a look at this example.

```asp
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master" AutoEventWireup="true"
    CodeBehind="Default.aspx.cs" Inherits="WebApplication1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">

    <script type="text/javascript" language="javascript">
        function ButtonClicked() {
            var element = document.getElementById('<%= MyTextBox.ClientID %>');
            alert('Value: ' + element.id);
        }
    </script>
   
   <asp:TextBox ID="MyTextBox" runat="server" />
   <button onclick="javascript:ButtonClicked()">Click Here</button>
</asp:Content>
```

In this example the Id of MyTextBox will be showed in a messagebox. Because MyTextBox is added in a Asp:Content control, the Id of this textbox will be prefixed with something. When we run the site and look at the source of the webpage, we will see it's id.

```html
<script type="text/javascript" language="javascript">
        function ButtonClicked() {
            var element = document.getElementById('MainContentMyTextBox');
            alert('Value: ' + element.id);
            }    
</script>   
   <input name="ctl00$MainContent$MyTextBox" type="text" value="dfd" id="MainContentMyTextBox" />
   <button onclick="javascript:ButtonClicked()">Click Here</button>
```

##But how do we do this in ASP.NET MVC?

Take a look at this example. We use the TextBoxFor extension method to generate a textbox for our model. How do we figure out the Id that is used for the textbox?

```aspmvc
@model MvcApplication2.Models.Person

@{
    ViewBag.Title = "Home Page";
}

<h2>@ViewBag.Message</h2>
<p>
   <script type="text/javascript" language="javascript">
       function ButtonClicked() {
           var element = document.getElementById("????");
           alert("Value: ??");
           }
   </script>

   @Html.TextBoxFor(m => m.Name);
   <button onclick="javascript:ButtonClicked()">Click Here</button>
</p>
```

You can hardcode the Id of the textbox ( "Name" ) in the javascript, but would it not be a lot more beautiful or easier ( intellisense) if you could pas a lambda expression?

After looking in Reflector i saw that MVC uses this static class for generating an Id and it passes this Id to a static method of the TagBuilder class called "CreateSanitizedId".

Now that we know this, the only thing left to do is to create an HtmlHelper extension that will do exactly the same.

```csharp
namespace System.Web.Mvc.Html
{
    public static class IdHtmlHelper
    {
        public static String GetIdFor<TModel, TProperty>(this HtmlHelper<TModel> htmlHelper, Expression<Func<TModel, TProperty>> expression)
        {
            return TagBuilder.CreateSanitizedId(ExpressionHelper.GetExpressionText(expression));
        }
    }
}
```
Now we can write our code like this:

```aspmvc
@model MvcApplication2.Models.Person
@{
    ViewBag.Title = "Home Page";
  }

<h2>@ViewBag.Message</h2>
<p>
   <script type="text/javascript" language="javascript">
       function ButtonClicked() {
           var element = document.getElementById("@Html.GetIdFor( m=> m.Name)");
           alert("Value: " + element.id);        
       }
   </script>

  @Html.TextBoxFor( m => m.Name)
  <button onclick="javascript:ButtonClicked()">Click Here</button>
</p>
```

When we render the page and look at the source, we can see that the Id is put in the getElementById method.

```html
<p>
   <script type="text/javascript" language="javascript">
       function ButtonClicked() {
           var element = document.getElementById("Name");
           alert("Value: " + element.id);        
       }
   </script>
   <input id="Name" name="Name" type="text" value="John Doe" />
   <button onclick="javascript:ButtonClicked()">Click Here</button>
</p>
```

---
layout: post
author: Kenny Tordeur
title: "Submitting to different controller actions in ASP.NET MVC"
date: 2015-03-09 09:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC"]
alias: ["/post/Submitting-to-different-controller-actions-in-ASPNET-MVC", "/post/submitting-to-different-controller-actions-in-aspnet-mvc"]
redirect_from: "/post/submitting-to-different-controller-actions-in-aspnet-mvc"
--
When you create a view in ASP.NET MVC, you'll probably have 2 methods for that view in your controller. A HTTPGET method and a HTTPPOST method that will accept your form data. This POST method will do some sort of action. Now sometimes you wish, that you could put 2 submit buttons in the same form that will submit to 2 different actions on your controller.


##Solution one: Javascript to the rescue

You could use javascript to modify the action of your form. You'll add a click event on the submit button and overwrite the default submit action.

```html
<html>
 <body>
    <form id="myform">
           <input type="submit" id="submit1"/>
           <input type="submit" id="submit2"/>
    </form>
 </body>
 <script>
    $("#submit1").click(function() {
    document.myform.action = “http://localhost/controller/action1”;
    });
    $("#submit2").click(function() {
      document.myform.action = “http://localhost/controller/action2”; 
    });
 </script>
</html>
```

##Solution two: Creating a custom ActionNameSelector attribute

You can create a custom ActionNameSelector. This will select the correct action on your controller based on the value of your submit button. You can find a detailed blog [post](http://blog.maartenballiauw.be/post/2009/11/26/Supporting-multiple-submit-buttons-on-an-ASPNET-MVC-view.aspx) by Maarten Balliauw.

##Solution three: HTML 5 to the rescue

You can add a formaction action attribute on your submit button. This form attribute specifies where the form data needs to be sended when you click on the submit button.

```html
<html>
 <body>
    <form id="myform">
           <input type="submit" formaction="/controller/action1"/>
           <input type="submit" formaction="/controller/action2"/>
    </form>
 </body>
</html>
```

The downside of this solution, is that it isn't supported by all browsers.

![browsers supported](http://blog.kennytordeur.be/images/browsers.png)

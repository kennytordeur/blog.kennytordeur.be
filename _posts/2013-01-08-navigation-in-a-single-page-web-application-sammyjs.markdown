---
layout: post
title: "Navigation in a Single page Web application: Sammy.js"
date: 2013-01-08 13:15:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["Javascript", "Sammy.js"]
redirect_from: "/post/Navigation-in-a-Single-page-Web-application-Sammyjs"
alias: ["/post/Navigation-in-a-Single-page-Web-application-Sammyjs", "/post/navigation-in-a-single-page-web-application-sammyjs"]
---

Single page Web applications are becoming more and more frequent. There are a lot of frameworks/technologies that make the development of these applications a lot easier. You can use JQuery to manipulate your page, use Knockout.js to work the ViewModel way and use WebApi to get your data. All these tools make a fine toolbox to create responsive, single page web applications.

But how about the navigation in these applications? How can this be done in a pluggable, centralized way? You can easily create a JavaScript function on your page that will accept an id of a view that you want to show. This function will then hide all the others views, maybe using JQuery and some sort of animation, and then make the view you requested visible.

```html
<body>
    <a href="#" onclick="javascript:navigate('page_1')">Show Page 1</a>
    <a href="#" onclick="javascript:navigate('page_2')">Show Page 2</a>

    <div class="page" id="Div1" style="display: none">
        <h1>Page 1</h1>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per.
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    </div>

    <div class="page" id="Div2" style="display: none">
        <h1>Page 2</h1>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per. 
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    </div>

    <script type="text/javascript">
        function navigate(viewId) {
            $(".page").hide();

            $("#" + viewId).show("slow");
        }

    </script>
</body>
```

Now you have a function that is centralized and that actually is responsibly for showing the correct page (view). It acts like an ViewController. I'm not saying that this is a bad way of working, but there is one problem with this solution. The state of witch page was active isn&rsquo;t maintained. This means that you can't bookmark a specific page or send it to an external user. This is where Sammy.js comes to the rescue.

##Sammy.js
[Sammy.js](http://sammyjs.org/intro) is a small JavaScript framework that enables you to create routes in your page ( looks a lot like routes in MVC ). You can install it using Nuget.

```html
<body>
    <a href="#page_1">Show Page 1</a>
    <a href="#page_2">Show Page 2</a>

    <div class="page" id="page_1" style="display: none">
        <h1>Page 1</h1>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per.
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    </div>

    <div class="page" id="page_2" style="display: none">
        <h1>Page 2</h1>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per. 
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    </div>

    <script type="text/javascript">
        $(function () {
            Sammy(function () {
                this.get('#:page', function () {
                    var pageId = this.params.page;
                    navigate(pageId);
                });
            }).run();
        });

        function navigate(viewId) {
            $(".page").hide();
            $("#" + viewId).show("slow");
        }
    </script>
```

With Sammy.js, i created a route "#:page" where ":page" is actually a parameter. In the links, i removed the onclick attribute and added some href information. When you now click on the link, this information will be added to the url. Sammy.js will see this, and will try to match this to its predefined routes. It will then resolve the parameter from the url and pass it to the navigate function.

The end result is still the same but now the "state"; is added to the url so you can bookmark it or send it as a link.

![result](http://blog.kennytordeur.be/images/2013-01-08-navigation-in-a-single-page-web-application-sammyjs/image.png)

The solution can be downloaded [here](http://dl.dropbox.com/u/41091233/Blog/Navigation%20Single%20Page%20WebApp%20Sammy/Navigation%20Single%20Page%20WebApp%20Sammy.rar).

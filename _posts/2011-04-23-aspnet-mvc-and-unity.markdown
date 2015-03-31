---
layout: post
title: "ASP.NET MVC and Unity"
date: 2011-04-23 09:40:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC", "Unity"]
alias: ["/post/ASPNET-MVC-and-Unity", "/post/aspnet-mvc-and-unity"]
---
<!-- more -->
{% include imported_disclaimer.html %}
<h2>What is Unity?</h2>
<p>The Unity Application Block (Unity) is a lightweight extensible dependency injection container with support for constructor, property, and method call injection. Dependency injection is used to create loosely coupled applications. These kind of applications are easier to maintain and easier to test.</p>
<h2>&nbsp;</h2>
<h2>What do we want to achieve?</h2>
<p>We want to make our controllers loosely coupled and break any hard dependencies it has. Every object/resource that our controller needs, has to be automatically populated and correctly initiated by Unity.</p>
<h2>The Asp.net mvc application</h2>
<p>This is the HomeController that we have in our application.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:afa8fb37-51da-43c6-8ac0-a6dec950125a" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">HomeController</span> : <span style="color: #2b91af;">Controller</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">ITitleRepository</span> _titleRepository = <span style="color: #0000ff;">null</span>;<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> HomeController()<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;_titleRepository = <span style="color: #0000ff;">new</span> <span style="color: #2b91af;">TitleRepository</span>();<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #008000;">//</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #008000;">// GET: /Home/</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #2b91af;">ActionResult</span> Index()<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ViewBag.Title = _titleRepository.GetTitle(<span style="color: #a31515;">"Index"</span>);<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> View();<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> &nbsp;&nbsp;&nbsp;&nbsp;}</div>
</div>
</div>
<p>&nbsp;</p>
<p>As you can see, it uses a TitleRepository. This repository is used to determine which title has to be rendered on a specific page.&nbsp;&nbsp;</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:cceeb869-bf61-4020-8069-f75c71686029" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; max-height: 300px; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;_titleRepository = <span style="color: #0000ff;">new</span> <span style="color: #2b91af;">TitleRepository</span>();</div>
</div>
</div>
<p>&nbsp;</p>
<p>This is the line that we want to remove. With this, we make a hard dependency between our HomeController and the TitleRepository.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:030efde3-69ab-4c3b-a9aa-96e0357c8830" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">interface</span> <span style="color: #2b91af;">ITitleRepository</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">string</span> GetTitle(<span style="color: #2b91af;">String</span> page);<br /> &nbsp;&nbsp;&nbsp;&nbsp;}</div>
</div>
</div>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:7086647f-7192-4e86-98c6-4479a7d89080" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">TitleRepository</span> : MVCWebsite.Repository.<span style="color: #2b91af;">ITitleRepository</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #2b91af;">String</span> GetTitle(<span style="color: #2b91af;">String</span> page)<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> <span style="color: #2b91af;">String</span>.Format(<span style="color: #a31515;">" This is a Title for the page: {0}"</span>, page);<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> &nbsp;&nbsp;&nbsp;&nbsp;}</div>
</div>
</div>
<h2>&nbsp;</h2>
<h2>Using Unity</h2>
<h3>Adding references</h3>
<p>Add the Microsoft.Practices.Unity dll to you project. Unity can be found at <a href="http://unity.codeplex.com/">http://unity.codeplex.com/</a>.</p>
<h3>Creating a custom LifetimeManager</h3>
<p>When you register a type with Unity, you can also specify a lifetime of this type. If you don&rsquo;t specify a lifetime, Unity will create a new instance of that type each time that the type is requested. In an Asp.Net application, we can make the LifetimeManager cache or store the created objects in the HttpContext HttpSession or HttpApplication. In my case, i want a singleton per request.</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:ccc17280-ec48-489f-9ea6-2ec48f645cb4" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">HttpContextLifetimeManager</span>&lt;T&gt; : <span style="color: #2b91af;">LifetimeManager</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">override</span> <span style="color: #0000ff;">void</span> SetValue(<span style="color: #0000ff;">object</span> newValue)<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">HttpContext</span>.Current.Items[<span style="color: #0000ff;">typeof</span>(T).AssemblyQualifiedName] = newValue;<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">override</span> <span style="color: #0000ff;">object</span> GetValue()<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> <span style="color: #2b91af;">HttpContext</span>.Current.Items[<span style="color: #0000ff;">typeof</span>(T).AssemblyQualifiedName];<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">override</span> <span style="color: #0000ff;">void</span> RemoveValue()<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">HttpContext</span>.Current.Items.Remove(<span style="color: #0000ff;">typeof</span>(T).AssemblyQualifiedName);<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}&nbsp;&nbsp;<br /> &nbsp;&nbsp;&nbsp;&nbsp;}</div>
</div>
</div>
<p>&nbsp;</p>
<h3>Creating the UnityContainer</h3>
<p>Next in line is the creation of a UnityContainer. This container will be used to resolve certain types. In order to work, we have to register these types. In our case, we our going to register the ITitleRepository type with the TitleRepository type. This will mean that if we ask the container to resolve the ITitleRepository, it will return a TitleRepository object. As you see, i pass the HttpContextLifetimeManager as parameter.</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:22b71884-ac9d-439e-99c4-b017b2558182" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;"><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">UnityConfigurator</span><br /> {<br /> &nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #2b91af;">IUnityContainer</span> GetContainer()<br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">IUnityContainer</span> container = <span style="color: #0000ff;">new</span> <span style="color: #2b91af;">UnityContainer</span>();<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;container.RegisterType&lt;<span style="color: #2b91af;">ITitleRepository</span>, <span style="color: #2b91af;">TitleRepository</span>&gt;(<span style="color: #0000ff;">new</span> <span style="color: #2b91af;">HttpContextLifetimeManager</span>&lt;<span style="color: #2b91af;">ITitleRepository</span>&gt;());<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> container;<br /> &nbsp;&nbsp;&nbsp;&nbsp;}<br /> }</div>
</div>
</div>
<p>&nbsp;</p>
<p>Now we are registering our type hardcoded. It is also possible doing this in the config file.</p>
<h3>Creating a Controller Factory</h3>
<p>We need to create a custom Controller factory to replace the default factory that is used by the MVC framework. Our factory accepts a IUnityContainer as parameter. This container contains all the types we registered.</p>
<p>We only need to override the GetControllerInstance method. In this method we have to use the container to create an instance of the requested controller. This way, Unity will automatically resolve the requested types for this controller. In our case: ITitleRepository.</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:bea68f1f-75e2-4b0b-95bc-844e4bfbc273" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">UnityControllerFactory</span> : <span style="color: #2b91af;">DefaultControllerFactory</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">IUnityContainer</span> container = <span style="color: #0000ff;">null</span>;<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> UnityControllerFactory(<span style="color: #2b91af;">IUnityContainer</span> container)<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">this</span>.container = container;<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">override</span> <span style="color: #2b91af;">IController</span> GetControllerInstance(System.Web.Routing.<span style="color: #2b91af;">RequestContext</span> requestContext, <span style="color: #2b91af;">Type</span> controllerType)<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">IController</span> result = <span style="color: #0000ff;">null</span>;<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">if</span> (<span style="color: #0000ff;">null</span> != controllerType)<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;result = container.Resolve(controllerType) <span style="color: #0000ff;">as</span> <span style="color: #2b91af;">IController</span>;<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> result;<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> &nbsp;&nbsp;&nbsp;&nbsp;}</div>
</div>
</div>
<p><br /><br />Now we have to tell the MVC framework to use the UnityControllerFactory as it&rsquo;s default controller factory. This is done best in the global.asax, in the Application_Start method.</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:c2eac5f6-841c-453c-a898-1ac258a7d879" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;"><span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">void</span> Application_Start()<br /> {<br /> &nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">AreaRegistration</span>.RegisterAllAreas();<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;RegisterGlobalFilters(<span style="color: #2b91af;">GlobalFilters</span>.Filters);<br /> &nbsp;&nbsp;&nbsp;&nbsp;RegisterRoutes(<span style="color: #2b91af;">RouteTable</span>.Routes);<br /> &nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">ControllerBuilder</span>.Current.SetControllerFactory(<span style="color: #0000ff;">new</span> <span style="color: #2b91af;">UnityControllerFactory</span>(<span style="color: #2b91af;">UnityConfigurator</span>.GetContainer()));<br /> }</div>
</div>
</div>
<h2>&nbsp;</h2>
<h2>Breaking the dependency</h2>
<p>Now we have to modify our HomeController. We change it&rsquo;s constructor to accept an object of the ITitleRepository type.</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:578d53b0-8feb-47e0-8c65-83e0823d1835" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">HomeController</span> : <span style="color: #2b91af;">Controller</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">ITitleRepository</span> _titleRepository = <span style="color: #0000ff;">null</span>;<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> HomeController(<span style="color: #2b91af;">ITitleRepository</span> titleRepository)<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;_titleRepository = titleRepository;<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #008000;">//</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #008000;">// GET: /Home/</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #2b91af;">ActionResult</span> Index()<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ViewBag.Title = _titleRepository.GetTitle(<span style="color: #a31515;">"Index"</span>);<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> View();<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> &nbsp;&nbsp;&nbsp;&nbsp;}</div>
</div>
</div>
<p>&nbsp;</p>
<h2>&nbsp;</h2>
<h2>The result</h2>
<p>When we go to the index of the HomeController, our UnityControllerFactory comes in action and Untity will inject the TitleRepository in the constructor of the HomeController.</p>
<p><a href="http://lh6.ggpht.com/_VX53QHr178k/TbKei-_o62I/AAAAAAAAAEQ/K__P64D6x5o/s1600-h/image3.png"><img style="display: inline; border-width: 0px;" title="" src="http://lh4.ggpht.com/_VX53QHr178k/TbKejV9olgI/AAAAAAAAAEU/GHmI2mrRl6c/image_thumb1.png?imgmax=800" alt="" width="504" height="158" border="0" /></a></p>
<p>When i want to use another implementation of the ITitleRepository, i only have to change the GetContainer method of the UnityConfigurator and the HomeController will automatically be injected with the new implementation.</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:e0052546-ce7e-4948-ab34-8aed19892ede" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px; white-space: nowrap;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">TitleRepository2</span> : MVCWebsite.Repository.<span style="color: #2b91af;">ITitleRepository</span><br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">string</span> GetTitle(<span style="color: #2b91af;">String</span> page)<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> <span style="color: #2b91af;">String</span>.Format(<span style="color: #a31515;">"{0} is the Title for this page!"</span>, page.ToUpper());<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br /> &nbsp;&nbsp;&nbsp;&nbsp;}</div>
</div>
</div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:a952e465-0eeb-4514-aba6-8d319c8468db" class="wlWriterEditableSmartContent" style="display: inline; float: none; margin: 0px; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background-color: #ffffff; overflow: auto; padding: 2px 5px;"><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> <span style="color: #2b91af;">UnityConfigurator</span><br /> {<br /> &nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #2b91af;">IUnityContainer</span> GetContainer()<br /> &nbsp;&nbsp;&nbsp;&nbsp;{<br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #2b91af;">IUnityContainer</span> container = <span style="color: #0000ff;">new</span> <span style="color: #2b91af;">UnityContainer</span>();<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;container.RegisterType&lt;<span style="color: #2b91af;">ITitleRepository</span>, <span style="color: #2b91af;">TitleRepository2</span>&gt;(<span style="color: #0000ff;">new</span> <span style="color: #2b91af;">HttpContextLifetimeManager</span>&lt;<span style="color: #2b91af;">ITitleRepository</span>&gt;());<br /> <br /> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="color: #0000ff;">return</span> container;<br /> &nbsp;&nbsp;&nbsp;&nbsp;}<br /> }</div>
</div>
</div>
<p>&nbsp;</p>
<p><a href="http://lh5.ggpht.com/_VX53QHr178k/TbKeju0U_1I/AAAAAAAAAEY/KcFxjzgikeA/s1600-h/image11.png"><img style="display: inline; border-width: 0px;" title="" src="http://lh3.ggpht.com/_VX53QHr178k/TbKekfc7Y-I/AAAAAAAAAEc/x33OjxXu8Cg/image_thumb5.png?imgmax=800" alt="" width="504" height="171" border="0" /></a></p>

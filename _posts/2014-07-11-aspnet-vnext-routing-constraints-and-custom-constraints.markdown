---
layout: post
title: "ASP.NET VNext: Routing constraints and custom constraints"
date: 2014-07-11 15:37:00 +0200
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET"]
alias: ["/post/ASPNET-VNext-Routing-constraints-and-custom-constraints", "/post/aspnet-vnext-routing-constraints-and-custom-constraints"]
---
<!-- more -->
{% include imported_disclaimer.html %}
<p>In ASP.NET VNext the routing system has been unified and it is build as a middleware component that easily can be plugged in.&nbsp; This routing system can be found in the “Microsoft.AspNet.Routing” Nuget Package or add <a href="https://github.com/aspnet/Routing">Github</a> if you want to work with the source code.</p> <h1>Constraints</h1> <p>Here is an example how we putted constraints on our route parameters in MVC 5. In this case the parameter “productId” must match an integer.</p><pre class="csharpcode">routes.MapRoute(
    <span class="str">"Product"</span>, 
    <span class="str">"Product/{productId}"</span>, 
    defaults: <span class="kwrd">new</span> { controller = <span class="str">"Product"</span>, action = <span class="str">"Details"</span> }, 
    constraints: <span class="kwrd">new</span> { productId = <span class="str">@"\d+"</span> });</pre>
<p>ASP.NET VNext introduced a new syntax for defining constraints:</p>
<p><span style="color: #c0504d; font-size: medium">“{parameter:constraint}”</span></p>
<p><span style="color: #c0504d; font-size: 12pt">parameter </span>will contain the name of the parameter you want to constraint and <span style="color: #c0504d; font-size: 12pt">constraint </span>will specify with constraint will be applied. </p>
<p><a href="/images/image_10.png"><img style="background-image: none; border-right-width: 0px; padding-left: 0px; padding-right: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px; padding-top: 0px" title="image" border="0" alt="image" src="/images/image_thumb_10.png" width="941" height="445"></a></p>
<p>For a full overview of the available constraints you can take a look at <a href="https://github.com/aspnet/Routing/tree/dev/src/Microsoft.AspNet.Routing/Constraints">Microsoft.AspNet.Routing/Constraints</a>.</p>
<p>So the previous constraint will look like this in ASP.NET VNext.</p><pre class="csharpcode">routes.MapRoute(
    <span class="str">"Product"</span>, 
    <span class="str">"Product/{productId:int}"</span>, 
    <span class="kwrd">new</span> { controller = <span class="str">"Product"</span>, action = <span class="str">"Details"</span> });</pre>
<p>You also can make the parameter “nullable” using <span style="color: #c0504d">“{parameter:constraint?}” </span>syntax.</p><pre class="csharpcode">routes.MapRoute(
    <span class="str">"Product"</span>, 
    <span class="str">"Product/{productId:int?}"</span>, 
    <span class="kwrd">new</span> { controller = <span class="str">"Product"</span>, action = <span class="str">"Details"</span> });</pre>
<p>Or pass in a default value <span style="color: #c0504d">“{parameter:constraint=value}”</span></p><pre class="csharpcode">routes.MapRoute(
    <span class="str">"Product"</span>, 
    <span class="str">"Product/{productId:int=1}"</span>, 
    <span class="kwrd">new</span> { controller = <span class="str">"Product"</span>, action = <span class="str">"Details"</span> });</pre>
<h1>Adding your custom constraints</h1>
<p>If the buildin constraints don’t meet your needs, you can also create a custom constraint based on the IRouteConstraint interface.</p><pre class="csharpcode"> <span class="kwrd">public</span> <span class="kwrd">class</span> AreYouLuckyRouteConstraint : IRouteConstraint
    {
        <span class="kwrd">public</span> <span class="kwrd">bool</span> Match(HttpContext httpContext, IRouter route, <span class="kwrd">string</span> routeKey, IDictionary&lt;<span class="kwrd">string</span>, <span class="kwrd">object</span>&gt; values, RouteDirection routeDirection)
        {
            <span class="kwrd">object</span> <span class="kwrd">value</span>;
            <span class="kwrd">if</span> (values.TryGetValue(routeKey, <span class="kwrd">out</span> <span class="kwrd">value</span>) &amp;&amp; <span class="kwrd">value</span> != <span class="kwrd">null</span>)
            {
                <span class="kwrd">int</span> result;
                var valueString = Convert.ToString(<span class="kwrd">value</span>, CultureInfo.InvariantCulture);
                var isInt =  Int32.TryParse(valueString, NumberStyles.Integer, CultureInfo.InvariantCulture, <span class="kwrd">out</span> result);

                <span class="kwrd">if</span> (isInt)
                {
                    Random rand = <span class="kwrd">new</span> Random(10);
                    <span class="kwrd">return</span> result == rand.Next(100);
                }
                <span class="kwrd">else</span>
                {
                    <span class="kwrd">return</span> <span class="kwrd">false</span>;
                }
            }
            <span class="kwrd">return</span> <span class="kwrd">false</span>;
        }
    }</pre>
<p>This route constraint will test how lucky you are and will only pass when you passed a correct integer value between the number 0 and 100. Now we need to add our custom constraint to the application route options in the Startup class.</p><pre class="csharpcode"> var routeOptions = app.ApplicationServices.GetService&lt;IOptionsAccessor&lt;RouteOptions&gt;&gt;();

 routeOptions.Options.ConstraintMap.Add(<span class="str">"areyoulucky"</span>, <span class="kwrd">typeof</span>(AreYouLuckyRouteConstraint));</pre>
<p>Now you can use your custom constraint.</p><pre class="csharpcode">routes.MapRoute(
    <span class="str">"Product"</span>, 
    <span class="str">"Product/{productId:areyoulucky}"</span>, 
    <span class="kwrd">new</span> { controller = <span class="str">"Product"</span>, action = <span class="str">"Details"</span> });</pre>
<style type="text/css">.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }
</style>

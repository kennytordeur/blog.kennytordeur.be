---
ID: 66
post_title: 'ASP.NET VNext: Routing constraints and custom constraints'
author: kennytordeur
post_date: 2014-07-11 13:37:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2014/07/11/asp-net-vnext-routing-constraints-and-custom-constraints/
published: true
---
In ASP.NET VNext the routing system has been unified and it is build as a middleware component that easily can be plugged in. This routing system can be found in the “Microsoft.AspNet.Routing” Nuget Package or add <a href="https://github.com/aspnet/Routing">github</a> if you want to work with the source code.

<h2>Constraints</h2>

Here is an example how we putted constraints on our route parameters in MVC 5. In this case the parameter “productId” must match an integer.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="n">routes</span><span class="p">.</span><span class="n">MapRoute</span><span class="p">(</span>
    <span class="s">&quot;Product&quot;</span><span class="p">,</span> 
    <span class="s">&quot;Product/{productId}&quot;</span><span class="p">,</span> 
    <span class="n">defaults</span><span class="p">:</span> <span class="k">new</span> <span class="p">{</span> <span class="n">controller</span> <span class="p">=</span> <span class="s">&quot;Product&quot;</span><span class="p">,</span> <span class="n">action</span> <span class="p">=</span> <span class="s">&quot;Details&quot;</span> <span class="p">},</span> 
    <span class="n">constraints</span><span class="p">:</span> <span class="k">new</span> <span class="p">{</span> <span class="n">productId</span> <span class="p">=</span> <span class="s">@&quot;\d+&quot;</span> <span class="p">});</span>
</code></pre></div>

ASP.NET VNext introduced a new syntax for defining constraints:</p>

<span style="color: #c0504d; font-size: medium">“{parameter:constraint}”</span>

<span style="color: #c0504d; font-size: 12pt">parameter </span>will contain the name of the parameter you want to constraint and <span style="color: #c0504d; font-size: 12pt">constraint </span>will specify with constraint will be applied.

<img src="http://blog.kennytordeur.be/images/2014-07-11-aspnet-vnext-routing-constraints-and-custom-constraints/constraints.png" alt="constraints">

For a full overview of the available constraints you can take a look at <a href="htpps://github.com/aspnet/Routing/tree/dev/src/Microsoft.AspNet.Routing/Constraints">Microsoft.AspNet.Routing/Constraints</a>.

So the previous constraint will look like this in ASP.NET VNext.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="n">routes</span><span class="p">.</span><span class="n">MapRoute</span><span class="p">(</span>
    <span class="s">&quot;Product&quot;</span><span class="p">,</span> 
    <span class="s">&quot;Product/{productId:int}&quot;</span><span class="p">,</span> 
    <span class="k">new</span> <span class="p">{</span> <span class="n">controller</span> <span class="p">=</span> <span class="s">&quot;Product&quot;</span><span class="p">,</span> <span class="n">action</span> <span class="p">=</span> <span class="s">&quot;Details&quot;</span> <span class="p">});</span>
</code></pre></div>

You also can make the parameter “nullable” using <span style="color: #c0504d">“{parameter:constraint?}” </span>syntax.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="n">routes</span><span class="p">.</span><span class="n">MapRoute</span><span class="p">(</span>
    <span class="s">&quot;Product&quot;</span><span class="p">,</span> 
    <span class="s">&quot;Product/{productId:int?}&quot;</span><span class="p">,</span> 
    <span class="k">new</span> <span class="p">{</span> <span class="n">controller</span> <span class="p">=</span> <span class="s">&quot;Product&quot;</span><span class="p">,</span> <span class="n">action</span> <span class="p">=</span> <span class="s">&quot;Details&quot;</span> <span class="p">});</span>
</code></pre></div>

Or pass in a default value <span style="color: #c0504d">“{parameter:constraint=value}”.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="n">routes</span><span class="p">.</span><span class="n">MapRoute</span><span class="p">(</span>
    <span class="s">&quot;Product&quot;</span><span class="p">,</span> 
    <span class="s">&quot;Product/{productId:int=1}&quot;</span><span class="p">,</span> 
    <span class="k">new</span> <span class="p">{</span> <span class="n">controller</span> <span class="p">=</span> <span class="s">&quot;Product&quot;</span><span class="p">,</span> <span class="n">action</span> <span class="p">=</span> <span class="s">&quot;Details&quot;</span> <span class="p">});</span>
</code></pre></div>

<h2>Adding your custom constraints</h2>

If the buildin constraints don’t meet your needs, you can also create a custom constraint based on the IRouteConstraint interface.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="k">public</span> <span class="k">class</span> <span class="nc">AreYouLuckyRouteConstraint</span> <span class="p">:</span> <span class="n">IRouteConstraint</span>
<span class="p">{</span>
    <span class="k">public</span> <span class="kt">bool</span> <span class="nf">Match</span><span class="p">(</span><span class="n">HttpContext</span> <span class="n">httpContext</span><span class="p">,</span> <span class="n">IRouter</span> <span class="n">route</span><span class="p">,</span> <span class="kt">string</span> <span class="n">routeKey</span><span class="p">,</span> <span class="n">IDictionary</span><span class="p">&lt;</span><span class="kt">string</span><span class="p">,</span> <span class="kt">object</span><span class="p">&gt;</span> <span class="n">values</span><span class="p">,</span> <span class="n">RouteDirection</span> <span class="n">routeDirection</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="kt">object</span> <span class="k">value</span><span class="p">;</span>
            <span class="k">if</span> <span class="p">(</span><span class="n">values</span><span class="p">.</span><span class="n">TryGetValue</span><span class="p">(</span><span class="n">routeKey</span><span class="p">,</span> <span class="k">out</span> <span class="k">value</span><span class="p">)</span> <span class="p">&amp;&amp;</span> <span class="k">value</span> <span class="p">!=</span> <span class="k">null</span><span class="p">)</span>
            <span class="p">{</span>
                <span class="kt">int</span> <span class="n">result</span><span class="p">;</span>
                <span class="kt">var</span> <span class="n">valueString</span> <span class="p">=</span> <span class="n">Convert</span><span class="p">.</span><span class="n">ToString</span><span class="p">(</span><span class="k">value</span><span class="p">,</span> <span class="n">CultureInfo</span><span class="p">.</span><span class="n">InvariantCulture</span><span class="p">);</span>
                <span class="kt">var</span> <span class="n">isInt</span> <span class="p">=</span>  <span class="n">Int32</span><span class="p">.</span><span class="n">TryParse</span><span class="p">(</span><span class="n">valueString</span><span class="p">,</span> <span class="n">NumberStyles</span><span class="p">.</span><span class="n">Integer</span><span class="p">,</span> <span class="n">CultureInfo</span><span class="p">.</span><span class="n">InvariantCulture</span><span class="p">,</span> <span class="k">out</span> <span class="n">result</span><span class="p">);</span>


            <span class="k">if</span><span class="p">(</span><span class="n">isInt</span><span class="p">)</span>
            <span class="p">{</span>
                <span class="n">Random</span> <span class="n">rand</span> <span class="p">=</span> <span class="k">new</span> <span class="n">Random</span><span class="p">(</span><span class="m">10</span><span class="p">);</span>
                <span class="k">return</span> <span class="n">result</span> <span class="p">==</span> <span class="n">rand</span><span class="p">.</span><span class="n">Next</span><span class="p">(</span><span class="m">100</span><span class="p">);</span>
            <span class="p">}</span>
            <span class="k">else</span>
            <span class="p">{</span>
               <span class="k">return</span> <span class="k">false</span><span class="p">;</span>
            <span class="p">}</span>
        <span class="p">}</span>
        <span class="k">return</span> <span class="k">false</span><span class="p">;</span>
    <span class="p">}</span>
<span class="p">}</span>
</code></pre></div>

This route constraint will test how lucky you are and will only pass when you passed a correct integer value between the number 0 and 100. Now we need to add our custom constraint to the application route options in the Startup class.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="kt">var</span> <span class="n">routeOptions</span> <span class="p">=</span> <span class="n">app</span><span class="p">.</span><span class="n">ApplicationServices</span><span class="p">.</span><span class="n">GetService</span><span class="p">&lt;</span><span class="n">IOptionsAccessor</span><span class="p">&lt;</span><span class="n">RouteOptions</span><span class="p">&gt;&gt;();</span>
    <span class="n">routeOptions</span><span class="p">.</span><span class="n">Options</span><span class="p">.</span><span class="n">ConstraintMap</span><span class="p">.</span><span class="n">Add</span><span class="p">(</span><span class="s">&quot;areyoulucky&quot;</span><span class="p">,</span> <span class="k">typeof</span><span class="p">(</span><span class="n">AreYouLuckyRouteConstraint</span><span class="p">));</span>
</code></pre></div>

Now you can use your custom constraint.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="n">routes</span><span class="p">.</span><span class="n">MapRoute</span><span class="p">(</span>
    <span class="s">&quot;Product&quot;</span><span class="p">,</span> 
    <span class="s">&quot;Product/{productId:areyoulucky}&quot;</span><span class="p">,</span> 
    <span class="k">new</span> <span class="p">{</span> <span class="n">controller</span> <span class="p">=</span> <span class="s">&quot;Product&quot;</span><span class="p">,</span> <span class="n">action</span> <span class="p">=</span> <span class="s">&quot;Details&quot;</span> <span class="p">});</span>
</code></pre></div>
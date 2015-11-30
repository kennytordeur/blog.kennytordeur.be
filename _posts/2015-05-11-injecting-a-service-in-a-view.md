---
ID: 63
post_title: Injecting a service in a view
author: kennytordeur
post_date: 2015-05-11 08:14:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2015/05/11/injecting-a-service-in-a-view/
published: true
---
In the ASP.NET MVC 6, you can now use the <em>@inject</em> keyword. This allows you to inject a service (class) into your view. This class must be public, non-nested and non-abstract.

The <em>@inject</em> needs 2 &quot;parameters&quot; in order to work.

<strong>@inject <em>[name of your class] [variable name that can be used in your view]</em></strong>

<h2>My Qoutes service</h2>

This is the class that i want to inject into my view.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="k">using</span> <span class="nn">System.Collections.Generic</span><span class="p">;</span>
<span class="k">using</span> <span class="nn">System</span><span class="p">;</span>

<span class="k">namespace</span> <span class="nn">VSCode1.Models</span>
<span class="p">{</span>
    <span class="k">public</span> <span class="k">class</span> <span class="nc">Quotes</span>
    <span class="p">{</span>
        <span class="n">List</span><span class="p">&lt;</span><span class="kt">string</span><span class="p">&gt;</span> <span class="n">_quotes</span> <span class="p">=</span> <span class="k">new</span> <span class="n">List</span><span class="p">&lt;</span><span class="kt">string</span><span class="p">&gt;</span> <span class="p">{</span> 
            <span class="s">&quot;Don&#39;t cry because it&#39;s over, smile because it happened&quot;</span><span class="p">,</span>
            <span class="s">&quot;Be yourself; everyone else is already taken.&quot;</span><span class="p">,</span>
            <span class="s">&quot;Two things are infinite: the universe and human stupidity; and I&#39;m not sure about the universe.&quot;</span>    
         <span class="p">};</span>

        <span class="k">public</span> <span class="kt">string</span> <span class="nf">GetRandomQuote</span><span class="p">()</span>
        <span class="p">{</span>
            <span class="n">Random</span> <span class="n">r</span> <span class="p">=</span> <span class="k">new</span> <span class="n">Random</span><span class="p">();</span>                
            <span class="k">return</span> <span class="n">_quotes</span><span class="p">[</span><span class="n">r</span><span class="p">.</span><span class="n">Next</span><span class="p">(</span><span class="m">0</span><span class="p">,</span> <span class="n">_quotes</span><span class="p">.</span><span class="n">Count</span> <span class="p">-</span> <span class="m">1</span><span class="p">)];</span>
        <span class="p">}</span>

        <span class="k">public</span> <span class="n">List</span><span class="p">&lt;</span><span class="kt">string</span><span class="p">&gt;</span> <span class="n">GetQuotes</span><span class="p">()</span>
        <span class="p">{</span>
            <span class="k">return</span> <span class="n">_quotes</span><span class="p">;</span>
        <span class="p">}</span>
    <span class="p">}</span>
<span class="p">}</span>
</code></pre></div>

<h2>Putting it in my view</h2>

To use the service in my view, i add the inject statement on top of the view. &quot;quotes&quot; can be used a variable in this view and is of type &quot;VSCode1.Models.Quotes&quot;.

<div class="highlight"><pre><code class="language-html" data-lang="html">@inject VSCode1.Models.Quotes quotes

<span class="nt">&lt;h1&gt;</span>@quotes.GetRandomQuote()<span class="nt">&lt;/h1&gt;</span>

@foreach(var quote in quotes.GetQuotes())
{
    <span class="nt">&lt;h2&gt;</span>@quote<span class="nt">&lt;/h2&gt;</span>
}
</code></pre></div>

<h2>Registering your service in your application</h2>

If i run my application, i would get an error saying that there is no service for type VSCode1.Models.Quotes.

<img src="http://blog.kennytordeur.be/images/2015-05-22-Injecting-a-service-in-a-view/error.png" alt="error">

When you use <em>@inject</em>, the runtime will use dependency injection to inject the type you requested. So your type needs to be registered in the dependency injector. This needs to be done in the <em>startup.cs</em> file.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">        <span class="k">public</span> <span class="k">void</span> <span class="nf">ConfigureServices</span><span class="p">(</span><span class="n">IServiceCollection</span> <span class="n">services</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="c1">//other stuff ...</span>

            <span class="n">services</span><span class="p">.</span><span class="n">AddTransient</span><span class="p">&lt;</span><span class="n">VSCode1</span><span class="p">.</span><span class="n">Models</span><span class="p">.</span><span class="n">Quotes</span><span class="p">&gt;();</span>
        <span class="p">}</span>
</code></pre></div>

Now the View renders without error.
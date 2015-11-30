---
ID: 69
post_title: 'Navigation in a Single page Web application: Sammy.js'
author: kennytordeur
post_date: 2013-01-08 12:15:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2013/01/08/navigation-in-a-single-page-web-application-sammy-js/
published: true
---
Single page Web applications are becoming more and more frequent. There are a lot of frameworks/technologies that make the development of these applications a lot easier. You can use JQuery to manipulate your page, use Knockout.js to work the ViewModel way and use WebApi to get your data. All these tools make a fine toolbox to create responsive, single page web applications.

But how about the navigation in these applications? How can this be done in a pluggable, centralized way? You can easily create a JavaScript function on your page that will accept an id of a view that you want to show. This function will then hide all the others views, maybe using JQuery and some sort of animation, and then make the view you requested visible.

<div class="highlight"><pre><code class="language-html" data-lang="html"><span class="nt">&lt;body&gt;</span>
    <span class="nt">&lt;a</span> <span class="na">href=</span><span class="s">&quot;#&quot;</span> <span class="na">onclick=</span><span class="s">&quot;javascript:navigate(&#39;page_1&#39;)&quot;</span><span class="nt">&gt;</span>Show Page 1<span class="nt">&lt;/a&gt;</span>
    <span class="nt">&lt;a</span> <span class="na">href=</span><span class="s">&quot;#&quot;</span> <span class="na">onclick=</span><span class="s">&quot;javascript:navigate(&#39;page_2&#39;)&quot;</span><span class="nt">&gt;</span>Show Page 2<span class="nt">&lt;/a&gt;</span>

    <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">&quot;page&quot;</span> <span class="na">id=</span><span class="s">&quot;Div1&quot;</span> <span class="na">style=</span><span class="s">&quot;display: none&quot;</span><span class="nt">&gt;</span>
        <span class="nt">&lt;h1&gt;</span>Page 1<span class="nt">&lt;/h1&gt;</span>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per.
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    <span class="nt">&lt;/div&gt;</span>

    <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">&quot;page&quot;</span> <span class="na">id=</span><span class="s">&quot;Div2&quot;</span> <span class="na">style=</span><span class="s">&quot;display: none&quot;</span><span class="nt">&gt;</span>
        <span class="nt">&lt;h1&gt;</span>Page 2<span class="nt">&lt;/h1&gt;</span>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per. 
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    <span class="nt">&lt;/div&gt;</span>

    <span class="nt">&lt;script </span><span class="na">type=</span><span class="s">&quot;text/javascript&quot;</span><span class="nt">&gt;</span>
        <span class="kd">function</span> <span class="nx">navigate</span><span class="p">(</span><span class="nx">viewId</span><span class="p">)</span> <span class="p">{</span>
            <span class="nx">$</span><span class="p">(</span><span class="s2">&quot;.page&quot;</span><span class="p">).</span><span class="nx">hide</span><span class="p">();</span>

            <span class="nx">$</span><span class="p">(</span><span class="s2">&quot;#&quot;</span> <span class="o">+</span> <span class="nx">viewId</span><span class="p">).</span><span class="nx">show</span><span class="p">(</span><span class="s2">&quot;slow&quot;</span><span class="p">);</span>
        <span class="p">}</span>

    <span class="nt">&lt;/script&gt;</span>
<span class="nt">&lt;/body&gt;</span>
</code></pre></div>

Now you have a function that is centralized and that actually is responsibly for showing the correct page (view). It acts like an ViewController. I&#39;m not saying that this is a bad way of working, but there is one problem with this solution. The state of witch page was active isn&rsquo;t maintained. This means that you can&#39;t bookmark a specific page or send it to an external user. This is where Sammy.js comes to the rescue.

<h2>Sammy.js</h2>

<a href="http://sammyjs.org/intro">Sammy.js</a> is a small JavaScript framework that enables you to create routes in your page ( looks a lot like routes in MVC ). You can install it using Nuget.

<div class="highlight"><pre><code class="language-html" data-lang="html"><span class="nt">&lt;body&gt;</span>
    <span class="nt">&lt;a</span> <span class="na">href=</span><span class="s">&quot;#page_1&quot;</span><span class="nt">&gt;</span>Show Page 1<span class="nt">&lt;/a&gt;</span>
    <span class="nt">&lt;a</span> <span class="na">href=</span><span class="s">&quot;#page_2&quot;</span><span class="nt">&gt;</span>Show Page 2<span class="nt">&lt;/a&gt;</span>

    <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">&quot;page&quot;</span> <span class="na">id=</span><span class="s">&quot;page_1&quot;</span> <span class="na">style=</span><span class="s">&quot;display: none&quot;</span><span class="nt">&gt;</span>
        <span class="nt">&lt;h1&gt;</span>Page 1<span class="nt">&lt;/h1&gt;</span>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per.
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    <span class="nt">&lt;/div&gt;</span>

    <span class="nt">&lt;div</span> <span class="na">class=</span><span class="s">&quot;page&quot;</span> <span class="na">id=</span><span class="s">&quot;page_2&quot;</span> <span class="na">style=</span><span class="s">&quot;display: none&quot;</span><span class="nt">&gt;</span>
        <span class="nt">&lt;h1&gt;</span>Page 2<span class="nt">&lt;/h1&gt;</span>
        Lorem ipsum dolor sit amet, eos augue fierent id. Quo nonumes blandit ei, ut sensibus contentiones per. 
        Nam te aliquam salutandi, sit at congue pertinax adversarium, eam putent verear laboramus ad. Ut viderer 
        legimus singulis duo, mel propriae volutpat ad. Mea suscipit gloriatur id, vel cu harum scaevola appetere.
    <span class="nt">&lt;/div&gt;</span>

    <span class="nt">&lt;script </span><span class="na">type=</span><span class="s">&quot;text/javascript&quot;</span><span class="nt">&gt;</span>
        <span class="nx">$</span><span class="p">(</span><span class="kd">function</span> <span class="p">()</span> <span class="p">{</span>
            <span class="nx">Sammy</span><span class="p">(</span><span class="kd">function</span> <span class="p">()</span> <span class="p">{</span>
                <span class="k">this</span><span class="p">.</span><span class="nx">get</span><span class="p">(</span><span class="s1">&#39;#:page&#39;</span><span class="p">,</span> <span class="kd">function</span> <span class="p">()</span> <span class="p">{</span>
                    <span class="kd">var</span> <span class="nx">pageId</span> <span class="o">=</span> <span class="k">this</span><span class="p">.</span><span class="nx">params</span><span class="p">.</span><span class="nx">page</span><span class="p">;</span>
                    <span class="nx">navigate</span><span class="p">(</span><span class="nx">pageId</span><span class="p">);</span>
                <span class="p">});</span>
            <span class="p">}).</span><span class="nx">run</span><span class="p">();</span>
        <span class="p">});</span>

        <span class="kd">function</span> <span class="nx">navigate</span><span class="p">(</span><span class="nx">viewId</span><span class="p">)</span> <span class="p">{</span>
            <span class="nx">$</span><span class="p">(</span><span class="s2">&quot;.page&quot;</span><span class="p">).</span><span class="nx">hide</span><span class="p">();</span>
            <span class="nx">$</span><span class="p">(</span><span class="s2">&quot;#&quot;</span> <span class="o">+</span> <span class="nx">viewId</span><span class="p">).</span><span class="nx">show</span><span class="p">(</span><span class="s2">&quot;slow&quot;</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="nt">&lt;/script&gt;</span>
</code></pre></div>

With Sammy.js, i created a route &quot;#:page&quot; where &quot;:page&quot; is actually a parameter. In the links, i removed the onclick attribute and added some href information. When you now click on the link, this information will be added to the url. Sammy.js will see this, and will try to match this to its predefined routes. It will then resolve the parameter from the url and pass it to the navigate function.

The end result is still the same but now the &quot;state&quot;; is added to the url so you can bookmark it or send it as a link.

<img src="http://blog.kennytordeur.be/images/2013-01-08-navigation-in-a-single-page-web-application-sammyjs/image.png" alt="result">

The solution can be downloaded <a href="http://dl.dropbox.com/u/41091233/Blog/Navigation%20Single%20Page%20WebApp%20Sammy/Navigation%20Single%20Page%20WebApp%20Sammy.rar">here</a>.
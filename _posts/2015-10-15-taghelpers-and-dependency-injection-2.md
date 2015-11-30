---
ID: 40
post_title: Taghelpers and Dependency Injection
author: kennytordeur
post_date: 2015-10-15 08:14:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2015/10/15/taghelpers-and-dependency-injection-2/
published: true
---
I was wondering if you could use Dependency Injection for taghelpers. I&#39;ll modify the example used in this <a href="/post/2015/10/14/creating-a-custom-taghelper">post</a>.

<h2>Creating a service to inject</h2>

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">interface</span> <span class="n">IPersonService</span>
    <span class="p">{</span>
        <span class="kt">string</span> <span class="nf">GetName</span><span class="p">();</span>
    <span class="p">}</span>

    <span class="k">public</span> <span class="k">class</span> <span class="nc">PersonService</span> <span class="p">:</span> <span class="n">IPersonService</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="n">String</span> <span class="nf">GetName</span><span class="p">()</span>
        <span class="p">{</span>
            <span class="k">return</span> <span class="s">&quot;PersonService.GetName()&quot;</span><span class="p">;</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

Register this class in the DI-system of Asp.net. Add it to services collection in the ConfigureServices method from the Startup class.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">  <span class="k">public</span> <span class="k">void</span> <span class="nf">ConfigureServices</span><span class="p">(</span><span class="n">IServiceCollection</span> <span class="n">services</span><span class="p">)</span>
  <span class="p">{</span>
        <span class="n">services</span><span class="p">.</span><span class="n">AddTransient</span><span class="p">&lt;</span><span class="n">IPersonService</span><span class="p">,</span> <span class="n">PersonService</span><span class="p">&gt;();</span>
  <span class="p">}</span>
</code></pre></div>

<h2>Modifiying the taghelper</h2>

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="na">    [TargetElement(&quot;Person&quot;)]</span>
    <span class="k">public</span> <span class="k">class</span> <span class="nc">MyTagHelper</span> <span class="p">:</span> <span class="n">TagHelper</span>
    <span class="p">{</span>
        <span class="n">IPersonService</span> <span class="n">_personService</span><span class="p">;</span>

        <span class="k">public</span> <span class="nf">MyTagHelper</span><span class="p">(</span><span class="n">IPersonService</span> <span class="n">personService</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="n">_personService</span> <span class="p">=</span> <span class="n">personService</span><span class="p">;</span>
        <span class="p">}</span>

        <span class="k">public</span> <span class="k">override</span> <span class="k">void</span> <span class="nf">Process</span><span class="p">(</span><span class="n">TagHelperContext</span> <span class="n">context</span><span class="p">,</span> <span class="n">TagHelperOutput</span> <span class="n">output</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="n">output</span><span class="p">.</span><span class="n">Content</span><span class="p">.</span><span class="n">SetContent</span><span class="p">(</span><span class="n">_personService</span><span class="p">.</span><span class="n">GetName</span><span class="p">());</span>

            <span class="k">base</span><span class="p">.</span><span class="n">Process</span><span class="p">(</span><span class="n">context</span><span class="p">,</span> <span class="n">output</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

<h2>Testing</h2>

When I run the application, the DI-system will also inject dependencies for taghelpers.

<img src="/images/2015-10-15-Taghelpers%20and%20DI/result.png" alt="The result">
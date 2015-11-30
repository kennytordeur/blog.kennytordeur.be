---
ID: 64
post_title: 'What&#39;s new in C# 6.0: Productivity'
author: kennytordeur
post_date: 2015-04-02 08:14:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2015/04/02/whats-new-in-c-6-0-productivity/
published: true
---
Version 6 of C# contains a bunch of new feature that allows to speed up the productivity of the developer.

<h2>Feature 1 : static usings</h2>

The classes are structured by their usings. If you need a specific class, you&#39;ll need to add a using containing the namespace of that class. If you do this, you&#39;ll get access to all the classes that are defined in that using. With static usings, you can bring only a specific class into &quot;scope&quot; which will you access to all of it&#39;s static methods.

Consider following console application.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="k">using</span> <span class="nn">System</span><span class="p">;</span>

<span class="k">namespace</span> <span class="nn">ConsoleApplication8</span>
<span class="p">{</span>
    <span class="k">class</span> <span class="nc">Program</span>
    <span class="p">{</span>
        <span class="k">static</span> <span class="k">void</span> <span class="nf">Main</span><span class="p">(</span><span class="kt">string</span><span class="p">[]</span> <span class="n">args</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="n">Console</span><span class="p">.</span><span class="n">WriteLine</span><span class="p">(</span><span class="s">&quot;Hello world&quot;</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
<span class="p">}</span>
</code></pre></div>

If i use a static using for the &quot;Console&quot; class, i can do this.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="k">using</span> <span class="nn">static</span> <span class="n">System</span><span class="p">.</span><span class="n">Console</span><span class="p">;</span>

<span class="k">namespace</span> <span class="nn">ConsoleApplication8</span>
<span class="p">{</span>
    <span class="k">class</span> <span class="nc">Program</span>
    <span class="p">{</span>
        <span class="k">static</span> <span class="k">void</span> <span class="nf">Main</span><span class="p">(</span><span class="kt">string</span><span class="p">[]</span> <span class="n">args</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="n">WriteLine</span><span class="p">(</span><span class="s">&quot;Hello world&quot;</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
<span class="p">}</span>
</code></pre></div>

<h2>Feature 2 : ? a.k.a the elvis operator</h2>

Consider following object graph.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">class</span> <span class="nc">ObjectA</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="n">ObjectB</span> <span class="n">ObjectB</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>
    <span class="p">}</span>

    <span class="k">public</span> <span class="k">class</span> <span class="nc">ObjectB</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="n">ObjectC</span> <span class="n">ObjectC</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>
    <span class="p">}</span>

    <span class="k">public</span> <span class="k">class</span> <span class="nc">ObjectC</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Name</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

If you want to access the &quot;Name&quot; property of &quot;ObjectC&quot; via ObjectA, i&#39;ll write code that looks like this.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">void</span> <span class="nf">SomeMethod</span><span class="p">(</span><span class="n">ObjectA</span> <span class="n">objectA</span><span class="p">)</span>
    <span class="p">{</span>
        <span class="kt">string</span> <span class="n">name</span> <span class="p">=</span> <span class="k">null</span><span class="p">;</span>

        <span class="k">if</span> <span class="p">(</span><span class="k">null</span> <span class="p">!=</span> <span class="n">objectA</span><span class="p">.</span><span class="n">ObjectB</span><span class="p">)</span>
            <span class="k">if</span> <span class="p">(</span><span class="k">null</span> <span class="p">!=</span> <span class="n">objectA</span><span class="p">.</span><span class="n">ObjectB</span><span class="p">)</span>
                <span class="k">if</span> <span class="p">(</span><span class="k">null</span> <span class="p">!=</span> <span class="n">objectA</span><span class="p">.</span><span class="n">ObjectB</span><span class="p">.</span><span class="n">ObjectC</span><span class="p">)</span>
                    <span class="n">name</span> <span class="p">=</span> <span class="n">objectA</span><span class="p">.</span><span class="n">ObjectB</span><span class="p">.</span><span class="n">ObjectC</span><span class="p">.</span><span class="n">Name</span><span class="p">;</span>
    <span class="p">}</span>
</code></pre></div>

I&#39;ll needs those if-statements because you don&#39;t know if the child object isn&#39;t null. With the elvis operator ? you can remove all the if-statements and make the code a little more readable.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">void</span> <span class="nf">SomeMethod</span><span class="p">(</span><span class="n">ObjectA</span> <span class="n">objectA</span><span class="p">)</span>
    <span class="p">{</span>
        <span class="kt">string</span> <span class="n">name</span> <span class="p">=</span> <span class="n">objectA</span><span class="p">?.</span><span class="n">ObjectB</span><span class="p">?.</span><span class="n">ObjectC</span><span class="p">?.</span><span class="n">Name</span><span class="p">;</span>
    <span class="p">}</span>
</code></pre></div>

If one of the child objects is null, we&#39;ll get the default value for a &quot;string&quot; object.

<h2>Feature 3:  Expression Bodied Members</h2>

Expression Bodies Members give you the possibility to write short methods even shorter.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">class</span> <span class="nc">ObjectC</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Name</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="k">override</span> <span class="kt">string</span> <span class="nf">ToString</span><span class="p">()</span>
        <span class="p">{</span>
            <span class="k">return</span> <span class="n">Name</span><span class="p">;</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

The &quot;ToString&quot; method can be writing shorter using expressions.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">class</span> <span class="nc">ObjectC</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Name</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="k">override</span> <span class="kt">string</span> <span class="nf">ToString</span><span class="p">()</span> <span class="p">=&gt;</span> <span class="n">Name</span><span class="p">;</span>
    <span class="p">}</span>
</code></pre></div>

You can only do this for expression on a single line. If you would write it like this, it won&#39;t build and Visual Studio will give build errors.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">class</span> <span class="nc">ObjectC</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Name</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="k">override</span> <span class="kt">string</span> <span class="nf">ToString</span><span class="p">()</span> <span class="p">=&gt;</span>  
        <span class="p">{</span>
            <span class="k">return</span> <span class="n">Name</span><span class="p">;</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>
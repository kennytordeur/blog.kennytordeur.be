---
ID: 62
post_title: Creating a custom taghelper
author: kennytordeur
post_date: 2015-10-14 08:14:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2015/10/14/creating-a-custom-taghelper/
published: true
---
In this post i&#39;ll show how you can create a custom taghelper.

<h2>What are taghelpers?</h2>

Basically taghelpers do the same thing as the htmlhelpers we all know. If you use a htmlhelper in your view, you&#39;ll see that it is actually C# code. If you would use a taghelper, it would lean more to html than to C# code.

<h2>Defining your taghelper</h2>

Your custom taghelper will need to inherit from the TagHelper base class. This base class defines two virtual methods called Process and ProcessAsync. In these methods you&#39;ll write your taghelper logic.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">class</span> <span class="nc">MyTagHelper</span> <span class="p">:</span> <span class="n">TagHelper</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="k">override</span> <span class="k">void</span> <span class="nf">Process</span><span class="p">(</span><span class="n">TagHelperContext</span> <span class="n">context</span><span class="p">,</span> <span class="n">TagHelperOutput</span> <span class="n">output</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="k">base</span><span class="p">.</span><span class="n">Process</span><span class="p">(</span><span class="n">context</span><span class="p">,</span> <span class="n">output</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

In order to use your taghelper, i&#39;ll need to register it. This done in the &quot;_ViewImports.cshtml&quot; file that can be found in the View folder.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">      <span class="n">@addTagHelper</span> <span class="s">&quot;MyWebApplication.MyTagHelper, MyWebApplication&quot;</span>
</code></pre></div>

The last part is your dll name. The first part is the name (namespace + classname) of the taghelper you want to register. If you want to register all the taghelpers from a specific dll you can use a wildcard.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">      <span class="n">@addTagHelper</span> <span class="s">&quot;*, MyWebApplication&quot;</span>
</code></pre></div>

When you succesfully have registered your taghelper, i&#39;ll get intellisense for your taghelper in your views.

Next, you will need to specify a target html tag that will invoke your custom taghelper. This can be done in 2 ways:

<ul>
        <li>by naming convention
            <ul>
                <li> If your your taghelper is called TestTagHelper, it will target all Test-tags</li>
            </ul>
        </li>
    </ul>

<pre><code>&lt;ul&gt;
    &lt;li&gt;using the TargetElement attribute
        &lt;ul&gt;
            &lt;li&gt;you explicitly set the target tag&lt;/li&gt;
        &lt;/ul&gt;
    &lt;/li&gt;
&lt;/ul&gt;
</code></pre>

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="na">    [TargetElement(&quot;Person&quot;)]</span>
    <span class="k">public</span> <span class="k">class</span> <span class="nc">MyTagHelper</span> <span class="p">:</span> <span class="n">TagHelper</span>
    <span class="p">{</span>        
        <span class="k">public</span> <span class="k">override</span> <span class="k">void</span> <span class="nf">Process</span><span class="p">(</span><span class="n">TagHelperContext</span> <span class="n">context</span><span class="p">,</span> <span class="n">TagHelperOutput</span> <span class="n">output</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="k">base</span><span class="p">.</span><span class="n">Process</span><span class="p">(</span><span class="n">context</span><span class="p">,</span> <span class="n">output</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

<h2>Adding attributes/properties on a taghelper</h2>

I now want to pass a name and lastname to the custom taghelper. These parameters will be passed like this:

<div class="highlight"><pre><code class="language-html" data-lang="html"><span class="nt">&lt;Person</span> <span class="na">name=</span><span class="s">&quot;kenny&quot;</span> <span class="na">lastname=</span><span class="s">&quot;tordeur&quot;</span><span class="nt">&gt;&lt;/Person&gt;</span>
</code></pre></div>

In the taghelper you will need to define these attributes. This can be done using the TargetElement tag.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="na">    [TargetElement(&quot;Person&quot;,Attributes = &quot;name,lastname&quot;)]</span>
    <span class="k">public</span> <span class="k">class</span> <span class="nc">MyTagHelper</span> <span class="p">:</span> <span class="n">TagHelper</span>
    <span class="p">{</span>        
        <span class="k">public</span> <span class="k">override</span> <span class="k">void</span> <span class="nf">Process</span><span class="p">(</span><span class="n">TagHelperContext</span> <span class="n">context</span><span class="p">,</span> <span class="n">TagHelperOutput</span> <span class="n">output</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="k">base</span><span class="p">.</span><span class="n">Process</span><span class="p">(</span><span class="n">context</span><span class="p">,</span> <span class="n">output</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

In order to get the values of the attributes, you can use the TagHelperContext. This object will contain all the defined attributes. You can also create properties that will map to the attribute names.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="na">    [TargetElement(&quot;Person&quot;,Attributes = &quot;name,lastname&quot;)]</span>
    <span class="k">public</span> <span class="k">class</span> <span class="nc">MyTagHelper</span> <span class="p">:</span> <span class="n">TagHelper</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Name</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="kt">string</span> <span class="n">Lastname</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="k">override</span> <span class="k">void</span> <span class="nf">Process</span><span class="p">(</span><span class="n">TagHelperContext</span> <span class="n">context</span><span class="p">,</span> <span class="n">TagHelperOutput</span> <span class="n">output</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="k">base</span><span class="p">.</span><span class="n">Process</span><span class="p">(</span><span class="n">context</span><span class="p">,</span> <span class="n">output</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

To map the attribute values to the correct properties, a naming convention is used. The attribute &quot;name&quot; will map to the property &quot;Name&quot;. So the first letter of the attribute will be transformed to an uppercased letter. If you would have an attribute &quot;last-name&quot;, it would be mapped to &quot;LastName&quot; property. So the &quot;-&quot; will be remove from the attribute name and the next letter will be transformed to an uppercased letter.

You aren&#39;t just limited by passing in simple types (string, int, ...) to the taghelper. You can also pass complex types to the taghelper.

<h2>Rendering some html</h2>

The only thing left to do is to implement an output for the taghelper. This can be done using the TagHelperOutput object. This object contains everything you need to create your custom html output. In this example i&#39;ll just concatent the Name with the Lastname.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp"><span class="na">    [TargetElement(&quot;Person&quot;,Attributes = &quot;name,lastname&quot;)]</span>
    <span class="k">public</span> <span class="k">class</span> <span class="nc">MyTagHelper</span> <span class="p">:</span> <span class="n">TagHelper</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Name</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="kt">string</span> <span class="n">Lastname</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="k">override</span> <span class="k">void</span> <span class="nf">Process</span><span class="p">(</span><span class="n">TagHelperContext</span> <span class="n">context</span><span class="p">,</span> <span class="n">TagHelperOutput</span> <span class="n">output</span><span class="p">)</span>
        <span class="p">{</span>
            <span class="n">output</span><span class="p">.</span><span class="n">Content</span><span class="p">.</span><span class="n">SetContent</span><span class="p">(</span><span class="err">$</span><span class="s">&quot;{Name}, {Lastname}&quot;</span><span class="p">);</span>

            <span class="k">base</span><span class="p">.</span><span class="n">Process</span><span class="p">(</span><span class="n">context</span><span class="p">,</span> <span class="n">output</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

<h2>The result</h2>

I&#39;ve but this in my view.

<div class="highlight"><pre><code class="language-html" data-lang="html">    <span class="nt">&lt;Person</span> <span class="na">lastname=</span><span class="s">&quot;Tordeur&quot;</span> <span class="na">name=</span><span class="s">&quot;Kenny&quot;</span><span class="nt">&gt;&lt;/Person&gt;</span>
</code></pre></div>

The intellisense helped me.

<img src="http://blog.kennytordeur.be/images/2015-10-14-creating-a-custom-taghelper/intellisense.png" alt="intellisense">

This is the output when the view is rendered in the browser.

<div class="highlight"><pre><code class="language-html" data-lang="html">    <span class="nt">&lt;Person&gt;</span>Kenny, Tordeur<span class="nt">&lt;/Person&gt;</span>
</code></pre></div>
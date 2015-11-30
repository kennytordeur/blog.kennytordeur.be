---
ID: 44
post_title: >
  Submitting to different controller
  actions in ASP.NET MVC
author: kennytordeur
post_date: 2015-03-09 08:14:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2015/03/09/submitting-to-different-controller-actions-in-asp-net-mvc-2/
published: true
---
When you create a view in ASP.NET MVC, you&#39;ll probably have 2 methods for that view in your controller. A HTTPGET method and a HTTPPOST method that will accept your form data. This POST method will do some sort of action. Now sometimes you wish, that you could put 2 submit buttons in the same form that will submit to 2 different actions on your controller.

<h2>Solution one: Javascript to the rescue</h2>

You could use javascript to modify the action of your form. You&#39;ll add a click event on the submit button and overwrite the default submit action.

<div class="highlight"><pre><code class="language-html" data-lang="html"><span class="nt">&lt;html&gt;</span>
 <span class="nt">&lt;body&gt;</span>
    <span class="nt">&lt;form</span> <span class="na">id=</span><span class="s">&quot;myform&quot;</span><span class="nt">&gt;</span>
           <span class="nt">&lt;input</span> <span class="na">type=</span><span class="s">&quot;submit&quot;</span> <span class="na">id=</span><span class="s">&quot;submit1&quot;</span><span class="nt">/&gt;</span>
           <span class="nt">&lt;input</span> <span class="na">type=</span><span class="s">&quot;submit&quot;</span> <span class="na">id=</span><span class="s">&quot;submit2&quot;</span><span class="nt">/&gt;</span>
    <span class="nt">&lt;/form&gt;</span>
 <span class="nt">&lt;/body&gt;</span>
 <span class="nt">&lt;script&gt;</span>
    <span class="nx">$</span><span class="p">(</span><span class="s2">&quot;#submit1&quot;</span><span class="p">).</span><span class="nx">click</span><span class="p">(</span><span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
    <span class="nb">document</span><span class="p">.</span><span class="nx">myform</span><span class="p">.</span><span class="nx">action</span> <span class="o">=</span> <span class="err">“</span><span class="nx">http</span><span class="o">:</span><span class="c1">//localhost/controller/action1”;</span>
    <span class="p">});</span>
    <span class="nx">$</span><span class="p">(</span><span class="s2">&quot;#submit2&quot;</span><span class="p">).</span><span class="nx">click</span><span class="p">(</span><span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
      <span class="nb">document</span><span class="p">.</span><span class="nx">myform</span><span class="p">.</span><span class="nx">action</span> <span class="o">=</span> <span class="err">“</span><span class="nx">http</span><span class="o">:</span><span class="c1">//localhost/controller/action2”; </span>
    <span class="p">});</span>
 <span class="nt">&lt;/script&gt;</span>
<span class="nt">&lt;/html&gt;</span>
</code></pre></div>

<h2>Solution two: Creating a custom ActionNameSelector attribute</h2>

You can create a custom ActionNameSelector. This will select the correct action on your controller based on the value of your submit button. You can find a detailed blog <a href="http://blog.maartenballiauw.be/post/2009/11/26/Supporting-multiple-submit-buttons-on-an-ASPNET-MVC-view.aspx">post</a> by Maarten Balliauw.

<h2>Solution three: HTML 5 to the rescue</h2>

You can add a formaction action attribute on your submit button. This form attribute specifies where the form data needs to be sended when you click on the submit button.

<div class="highlight"><pre><code class="language-html" data-lang="html"><span class="nt">&lt;html&gt;</span>
 <span class="nt">&lt;body&gt;</span>
    <span class="nt">&lt;form</span> <span class="na">id=</span><span class="s">&quot;myform&quot;</span><span class="nt">&gt;</span>
           <span class="nt">&lt;input</span> <span class="na">type=</span><span class="s">&quot;submit&quot;</span> <span class="na">formaction=</span><span class="s">&quot;/controller/action1&quot;</span><span class="nt">/&gt;</span>
           <span class="nt">&lt;input</span> <span class="na">type=</span><span class="s">&quot;submit&quot;</span> <span class="na">formaction=</span><span class="s">&quot;/controller/action2&quot;</span><span class="nt">/&gt;</span>
    <span class="nt">&lt;/form&gt;</span>
 <span class="nt">&lt;/body&gt;</span>
<span class="nt">&lt;/html&gt;</span>
</code></pre></div>

The downside of this solution, is that it isn&#39;t supported by all browsers.

<img src="http://blog.kennytordeur.be/images/2015-03-09-submitting-to-different-controller-actions-in-aspnet-mvc/browsers.png" alt="browsers supported">
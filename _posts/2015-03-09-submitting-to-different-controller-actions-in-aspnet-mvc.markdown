---
layout: post
author: Kenny Tordeur
title: "Submitting to different controller actions in ASP.NET MVC"
date: 2015-03-09 09:14:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC"]
alias: ["/post/Submitting-to-different-controller-actions-in-ASPNET-MVC", "/post/submitting-to-different-controller-actions-in-aspnet-mvc"]
---
<!-- more -->

{% include imported_disclaimer.html %}

<p>When you create a view in ASP.NET MVC, you&rsquo;ll probably have 2 methods for that view in your controller. A HTTPGET method and a HTTPPOST method that will accept your form data. This POST method will do some sort of action. Now sometimes you wish, that you could put 2 submit buttons in the same form that will submit to 2 different actions on your controller.</p>
<h1>Solution one: Javascript to the rescue</h1>
<p>You could use javascript to modify the action of your form. You&rsquo;ll add a click event on the submit button and overwrite the default submit action.</p>
<pre class="csharpcode"><span class="kwrd">&lt;</span><span class="html">html</span><span class="kwrd">&gt;</span>
 <span class="kwrd">&lt;</span><span class="html">body</span><span class="kwrd">&gt;</span>
    <span class="kwrd">&lt;</span><span class="html">form</span> <span class="attr">id</span><span class="kwrd">="myform"</span><span class="kwrd">&gt;</span>
           <span class="kwrd">&lt;</span><span class="html">input</span> <span class="attr">type</span><span class="kwrd">="submit"</span> <span class="attr">id</span><span class="kwrd">="submit1"</span><span class="kwrd">/&gt;</span>
           <span class="kwrd">&lt;</span><span class="html">input</span> <span class="attr">type</span><span class="kwrd">="submit"</span> <span class="attr">id</span><span class="kwrd">="submit2"</span><span class="kwrd">/&gt;</span>
    <span class="kwrd">&lt;/</span><span class="html">form</span><span class="kwrd">&gt;</span>
 <span class="kwrd">&lt;/</span><span class="html">body</span><span class="kwrd">&gt;</span>
 <span class="kwrd">&lt;</span><span class="html">script</span><span class="kwrd">&gt;</span>
    $(<span class="str">"#submit1"</span>).click(<span class="kwrd">function</span>() {
    document.myform.action = &ldquo;<span class="str"><a href="http://localhost/controller/action">http://localhost/controller/action</a>1&rdquo;;</span>
    });<br />    $(<span class="str">"#submit2"</span>).click(<span class="kwrd">function</span>() {<br />      document.myform.action = &ldquo;<span class="str"><a href="http://localhost/controller/action">http://localhost/controller/action</a>2&rdquo;;</span> <br />    });
 <span class="kwrd">&lt;/</span><span class="html">script</span><span class="kwrd">&gt;</span>
<span class="kwrd">&lt;/</span><span class="html">html</span><span class="kwrd">&gt;</span></pre>
<h1>Solution two:&nbsp; Creating a custom ActionNameSelector attribute</h1>
<p>You can create a custom ActionNameSelector. This will select the correct action on your controller based on the value of your submit button. You can find a detailed blog post <a href="http://blog.maartenballiauw.be/post/2009/11/26/Supporting-multiple-submit-buttons-on-an-ASPNET-MVC-view.aspx" target="_blank">here</a> by Maarten Balliauw.</p>
<h1>Solution three: HTML 5 to the rescue</h1>
<p>You can add a formaction action attribute on your submit button. This form attribute specifies where the form data needs to be sended when you click on the submit button.</p>
<pre class="csharpcode"><span class="kwrd">&lt;</span><span class="html">html</span><span class="kwrd">&gt;</span>
 <span class="kwrd">&lt;</span><span class="html">body</span><span class="kwrd">&gt;</span>
    <span class="kwrd">&lt;</span><span class="html">form</span> <span class="attr">id</span><span class="kwrd">="myform"</span><span class="kwrd">&gt;</span>
           <span class="kwrd">&lt;</span><span class="html">input</span> <span class="attr">type</span><span class="kwrd">="submit"</span> <span class="attr">formaction</span><span class="kwrd">="/controller/action1"</span><span class="kwrd">/&gt;</span>
           <span class="kwrd">&lt;</span><span class="html">input</span> <span class="attr">type</span><span class="kwrd">="submit"</span> <span class="attr">formaction</span><span class="kwrd">="/controller/action2"</span><span class="kwrd">/&gt;</span>
    <span class="kwrd">&lt;/</span><span class="html">form</span><span class="kwrd">&gt;</span>
 <span class="kwrd">&lt;/</span><span class="html">body</span><span class="kwrd">&gt;</span>
<span class="kwrd">&lt;/</span><span class="html">html</span><span class="kwrd">&gt;</span></pre>
<p>The downside of this solution, is that it isn&rsquo;t supported by all browsers.<a href="{{ '/images/image_11.png' | prepend: site.baseurl | prepend: site.url }}"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: block; float: none; margin-left: auto; margin-right: auto; padding-top: 0px; border-width: 0px;" title="image" src="{{ '/images/image_thumb_11.png' | prepend: site.baseurl | prepend: site.url }}" alt="image" width="765" height="109" border="0" /></a></p>

---
ID: 47
post_title: 'Asp.net MVC: The correct way to render a partialview'
author: kennytordeur
post_date: 2014-01-24 13:51:00
post_excerpt: ""
layout: post
permalink: >
  http://wp.kennytordeur.be/2014/01/24/asp-net-mvc-the-correct-way-to-render-a-partialview-2/
published: true
---
When you create an Asp.net mvc application will create partial views in order to use them as &quot;user controls&quot; throughout your application. But you have to use them with caution.

These are my viewmodels.

<div class="highlight"><pre><code class="language-csharp" data-lang="csharp">    <span class="k">public</span> <span class="k">class</span> <span class="nc">ParentViewModel</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Description</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="n">ChildViewModel</span> <span class="n">Child</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>

        <span class="k">public</span> <span class="nf">ParentViewModel</span><span class="p">()</span>
        <span class="p">{</span>
            <span class="n">Child</span> <span class="p">=</span> <span class="k">new</span> <span class="n">ChildViewModel</span><span class="p">();</span>
        <span class="p">}</span>
    <span class="p">}</span>

    <span class="k">public</span> <span class="k">class</span> <span class="nc">ChildViewModel</span>
    <span class="p">{</span>
        <span class="k">public</span> <span class="kt">string</span> <span class="n">Description</span> <span class="p">{</span> <span class="k">get</span><span class="p">;</span> <span class="k">set</span><span class="p">;</span> <span class="p">}</span>
    <span class="p">}</span>
</code></pre></div>

The ChildViewModel will be used on other different views, so I&#39;ll create a partial view for it. So my index.cshtml will look like this.

<img src="http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image.png" alt="image">

But when I submit my form, I won&#39;t get a Description for my ChildViewModel on the controller site. This is because the partial view for my ChildViewModel doesn&#39;t render like it should. The generate name for the Description field is wrong.

<img src="http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image_1.png" alt="image">

The &quot;Child&quot; prefix isn&#39;t added to the Description field so the modelbinder doesn&#39;t know that this is a property of the ChildViewModel.
Now when I convert my partial view to an editor template and use the EditorFor method the prefix will be added automatically.

<img src="http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image_2.png" alt="image">

The EditorFor method will actually add a &quot;TemplateInfo&quot; key to the ViewData of the editor template. This key will contain an instance of System.Web.Mvc.TemplateInfo. This object contains a HtmlFieldPrefix property that will be used by the TextBoxFor methode in order to generate the correct name.

So if we want to use the Partial method we&#39;ll need to pass a ViewDataDictionary with a TemplateInfo key that will contain the correct HtmlFieldPrefix.

<img src="http://blog.kennytordeur.be/images/2014-01-24-aspnet-mvc-the-correct-way-to-render-a-partialview/image_4.png" alt="image">

<h2>Conclusion</h2>

If your partialview needs to post data to the controller, I would suggest to transform your partialview to an editor template and use the EditorFor method. This will generate the correct HtmlFieldPrefix for you. But if you need to show a list and you need unique names. I would iterate the list and pass in a custom TemplateInfo to the Partail method for each item in the list. If you only need to show data you can use partial method.
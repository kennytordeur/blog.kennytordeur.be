---
layout: post
title: "Asp.net MVC: The correct way to render a partialview"
date: 2014-01-24 14:51:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["ASP.NET", "MVC"]
alias: ["/post/Aspnet-MVC-The-correct-way-to-render-a-partialview", "/post/aspnet-mvc-the-correct-way-to-render-a-partialview"]
---
<!-- more -->
{% include imported_disclaimer.html %}
<p>When you create an Asp.net mvc application will create partial views in order to use them as &ldquo;user controls&rdquo; throughout your application. But you have to use them with caution.</p>
<p>These are my viewmodels.</p>
<pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">class</span> ParentViewModel
    {
        <span class="kwrd">public</span> <span class="kwrd">string</span> Description { get; set; }

        <span class="kwrd">public</span> ChildViewModel Child { get; set; }

        <span class="kwrd">public</span> ParentViewModel()
        {
            Child = <span class="kwrd">new</span> ChildViewModel();
        }
    }

    <span class="kwrd">public</span> <span class="kwrd">class</span> ChildViewModel
    {
        <span class="kwrd">public</span> <span class="kwrd">string</span> Description { get; set; }
    }</pre>
<p>The ChildViewModel will be used on other different views, so &lsquo;ill create a partial view for it. So my index.cshtml will look like this.</p>
<p><a href="/images/image.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb.png" alt="image" width="672" height="249" border="0" /></a></p>
<p>But when I submit my form, I won&rsquo;t get a Description for my ChildViewModel on the controller site. This is because the partial view for my ChildViewModel doesn&rsquo;t render like it should. The generate name for the Description field is wrong.</p>
<p><a href="/images/image_1.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_1.png" alt="image" width="804" height="111" border="0" /></a></p>
<p>The &ldquo;Child&rdquo; prefix isn&rsquo;t added to the Description field so the modelbinder doesn&rsquo;t know that this is a property of the ChildViewModel.<br />Now when I convert my partial view to an editor template and use the EditorFor method the prefix will be added automatically.</p>
<p><a href="/images/image_2.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_2.png" alt="image" width="690" height="233" border="0" /></a></p>
<p>&nbsp;</p>
<p><a href="/images/image_3.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_3.png" alt="image" width="860" height="193" border="0" /></a></p>
<p>The EditorFor method will actually add a &ldquo;TemplateInfo&rdquo; key to the ViewData of the editor template. This key will contain an instance of System.Web.Mvc.TemplateInfo. This object contains a HtmlFieldPrefix property that will be used by the TextBoxFor methode in order to generate the correct name.</p>
<p>So if we want to use the Partial method we'll need to pass a ViewDataDictionary with a TemplateInfo key that will contain the correct HtmlFieldPrefix.</p>
<p><a href="/images/image_4.png"><img style="background-image: none; padding-left: 0px; padding-right: 0px; display: inline; padding-top: 0px; border-width: 0px;" title="image" src="/images/image_thumb_4.png" alt="image" width="890" height="255" border="0" /></a></p>
<h2>Conclusion</h2>
<p>If your partialview needs to post data to the controller, I would suggest to transform your partialview to an editor template and use the EditorFor method. This will generate the correct HtmlFieldPrefix for you.&nbsp; But if you need to show a list and you need unique names. I would iterate the list and pass in a custom TemplateInfo to the Partail method for each item in the list. If you only need to show data you can use partial method.</p>

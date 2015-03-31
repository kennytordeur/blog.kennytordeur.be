---
layout: post
title: "Asp.net MVC MEF: Per Request Lifetime"
date: 2012-12-21 08:52:00 +0100
comments: true
published: true
categories: ["blog", "archives"]
tags: ["MEF", "MVC 4"]
alias: ["/post/Aspnet-MVC-MEF-Per-Request-Lifetime", "/post/aspnet-mvc-mef-per-request-lifetime"]
---
<!-- more -->
{% include imported_disclaimer.html %}
<p>Like the title says, i want to use MEF in an Asp.Net MVC application and the lifetime of the object has to be exactly the same as the lifetime of the request. In short, i want 1 single unique object that is recreated per request.</p>
<p>In MEF there is a way to control the lifetime of the created object. This is specified the PartCreationPolicy attribute that you can use to decorate the object. In my test project i installed the nuget package i created in this <a href="http://kennytordeur.blogspot.be/#!/2012/11/nuget-package-integrated-mef-in-aspnet.html" target="_blank">post</a> to incorporate MEF in an Asp.net MVC application. I also created an object called MyCustomObject with an interface IMyCustomObject. This object has only one property called Created that contains the date when the object is created. In the constructor of that object i have but an thread.sleep. This way i am sure that the Created property will be different for every created object.</p>
<pre class="code"><span style="background: white; color: blue;">public interface </span><span style="background: white; color: #2b91af;">IMyCustomObject<br />    </span><span style="background: white; color: black;">{<br />        </span><span style="background: white; color: #2b91af;">DateTime </span><span style="background: white; color: black;">Created { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; </span><span style="background: white; color: blue;">set</span><span style="background: white; color: black;">; }<br />    }</span></pre>
<pre class="code"><span style="background: white; color: black;"> [</span><span style="background: white; color: #2b91af;">Export</span><span style="background: white; color: black;">(</span><span style="background: white; color: blue;">typeof</span><span style="background: white; color: black;">(</span><span style="background: white; color: #2b91af;">IMyCustomObject</span><span style="background: white; color: black;">))]    <br />    </span><span style="background: white; color: blue;">public class </span><span style="background: white; color: #2b91af;">MyCustomObject </span><span style="background: white; color: black;">: </span><span style="background: white; color: #2b91af;">IMyCustomObject<br />    </span><span style="background: white; color: black;">{<br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">DateTime </span><span style="background: white; color: black;">Created { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; </span><span style="background: white; color: blue;">set</span><span style="background: white; color: black;">; }<br /><br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: black;">MyCustomObject()<br />        {<br />            Created = </span><span style="background: white; color: #2b91af;">DateTime</span><span style="background: white; color: black;">.Now;<br />            System.Threading.</span><span style="background: white; color: #2b91af;">Thread</span><span style="background: white; color: black;">.Sleep(1000);<br />        }<br />    }</span></pre>
<p>&nbsp;</p>
<p>I also created an object called MyCustomObjectContainer. This object will contain a property of the type IMyCustomObject.</p>
<pre class="code"><span style="background: white; color: black;"> [</span><span style="background: white; color: #2b91af;">Export</span><span style="background: white; color: black;">]   <br />    </span><span style="background: white; color: blue;">public class </span><span style="background: white; color: #2b91af;">MyCustomObjectContainer<br />    </span><span style="background: white; color: black;">{<br />        [</span><span style="background: white; color: #2b91af;">Import</span><span style="background: white; color: black;">]<br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">IMyCustomObject </span><span style="background: white; color: black;">MyCustomObject { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; </span><span style="background: white; color: blue;">set</span><span style="background: white; color: black;">; }<br />    }</span></pre>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p><span style="background: white; color: black;">The controller will have 2 properties. The first of type IMyCustomObject aand the other of type MyCustomContainer. In the action method of the controller, i will write the Created value that is found in those 2 properties to the viewbag. That way i will be sure if the application is using the same object.</span></p>
<pre class="code"><span style="background: white; color: black;">    [</span><span style="background: white; color: #2b91af;">Export</span><span style="background: white; color: black;">]<br />    [</span><span style="background: white; color: #2b91af;">PartCreationPolicy</span><span style="background: white; color: black;">( </span><span style="background: white; color: #2b91af;">CreationPolicy</span><span style="background: white; color: black;">.NonShared)]<br />    </span><span style="background: white; color: blue;">public class </span><span style="background: white; color: #2b91af;">HomeController </span><span style="background: white; color: black;">: </span><span style="background: white; color: #2b91af;">Controller<br />    </span><span style="background: white; color: black;">{<br />        [</span><span style="background: white; color: #2b91af;">Import</span><span style="background: white; color: black;">]<br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">IMyCustomObject </span><span style="background: white; color: black;">MyCustomObject { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; </span><span style="background: white; color: blue;">set</span><span style="background: white; color: black;">; }<br /><br />        [</span><span style="background: white; color: #2b91af;">Import</span><span style="background: white; color: black;">]<br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">MyCustomObjectContainer </span><span style="background: white; color: black;">MyCustomObjectContainer { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; </span><span style="background: white; color: blue;">set</span><span style="background: white; color: black;">; }<br /><br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">ActionResult </span><span style="background: white; color: black;">Index()<br />        {<br />            </span><span style="background: white; color: blue;">this</span><span style="background: white; color: black;">.ViewBag.MyCustomtObjectDateCreated = MyCustomObject.Created;<br />            </span><span style="background: white; color: blue;">this</span><span style="background: white; color: black;">.ViewBag.MyCustomObjectContainerDateCreated = MyCustomObjectContainer.MyCustomObject.Created;<br />            </span><span style="background: white; color: blue;">return </span><span style="background: white; color: black;">View();<br />        }       <br />    }</span></pre>
<p>&nbsp;</p>
<h1>Choosing the right PartCreationPolicy</h1>
<p>&nbsp;</p>
<p>In MEF there are 3 different type of creation policies that we can choose. More information can be found <a href="http://mef.codeplex.com/wikipage?title=Parts%20Lifetime" target="_blank">here</a>.</p>
<p><br /><br /></p>
<ul>
<li><strong>Shared</strong>: the part author is telling MEF that at most one instance of the part may exist per container. </li>
<li><strong>NonShared</strong>: the part author is telling MEF that each request for exports of the part will be served by a new instance of it. </li>
<li><strong>Any or not supplied value</strong>: the part author allows the part to be used as either &ldquo;Shared&rdquo; or &ldquo;NonShared&rdquo;.</li>
</ul>
<p>&nbsp;</p>
<h2>The Shared Creation policy</h2>
<p>&nbsp;</p>
<p>We will set Creation policy to Shared and run the application.</p>
<p>&nbsp;</p>
<p><a href="http://lh5.ggpht.com/-GZqbCi9JAYs/UNQjLHKUaWI/AAAAAAAAAKA/qgKNZ6eVUr8/s1600-h/image%25255B5%25255D.png"><img style="display: block; float: none; margin-left: auto; margin-right: auto; border-width: 0px;" title="image" src="http://lh4.ggpht.com/-MZIQZm2nq4g/UNQjLyNc6tI/AAAAAAAAAKI/YYMV-t9F1JY/image_thumb%25255B3%25255D.png?imgmax=800" alt="image" width="915" height="123" border="0" /></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>We see that the same instance of the MyCustomOject is used. But when we refresh the page, we will get exactly the same value, over and over again. So this is not the behavior that we want.</p>
<p>&nbsp;</p>
<h2>The NonShared Creation Policy</h2>
<p>&nbsp;</p>
<p>We will set the creation policy to NonShared.</p>
<p>&nbsp;</p>
<p><a href="http://lh3.ggpht.com/-57KKKumaFL4/UNQjM8OjqEI/AAAAAAAAAKM/cockmoM78Ss/s1600-h/image%25255B10%25255D.png"><img style="display: block; float: none; margin-left: auto; margin-right: auto; border-width: 0px;" title="image" src="http://lh4.ggpht.com/-wpL3VaIWQcM/UNQjNwrQsiI/AAAAAAAAAKY/vLqqp5WM-Dg/image_thumb%25255B6%25255D.png?imgmax=800" alt="image" width="954" height="123" border="0" /></a></p>
<p>&nbsp;</p>
<p>With every refresh, we will get a different value for the Created property, but everytime that an IMyCustomObject is asked form the MEF container, a new value is created so this is not a solution for my problem.</p>
<p>&nbsp;</p>
<h1>&nbsp;</h1>
<p>&nbsp;</p>
<h1>The solution</h1>
<p>&nbsp;</p>
<p>We can create a solution for this problem using the Proxy-pattern, HttpContext and ExportFactory&lt;T&gt;.</p>
<p>&nbsp;</p>
<p>The ExportFactory&lt;T&gt; class was introduced in MEF 2. It allows us to create new instances and control the lifetime of those instances. This will be&nbsp; perfect to create the MyCustomObject object since we need to control the lifetime of this object.</p>
<p>&nbsp;</p>
<p>The HttpContext contains Http information about an HttpRequest. There is one property that is very interesting&nbsp; in our case, the Items property. This property is in fact a dictionary where you can insert objects into. This collection is cleared with every request (perfect). We will use this dictionary to add the created MyCustomObject. So every time that this object is requested, we will look in this dictionary. When it is not found, the ExportFactory&lt;T&gt; will create it and add it to the dictionary.</p>
<p>&nbsp;</p>
<p>The proxy pattern will be used to make this a transparent as possible.</p>
<p>&nbsp;</p>
<p><a href="http://lh3.ggpht.com/-RpVbsIC5Z7c/UNQjOitozWI/AAAAAAAAAKc/LPv_FRnFEGU/s1600-h/image%25255B16%25255D.png"><img style="display: block; float: none; margin-left: auto; margin-right: auto; border-width: 0px;" title="image" src="http://lh5.ggpht.com/-SbqjOaoYWqI/UNQjPQ17TKI/AAAAAAAAAKo/Axis60wIzkU/image_thumb%25255B10%25255D.png?imgmax=800" alt="image" width="680" height="279" border="0" /></a></p>
<p>&nbsp;</p>
<h2>The Implementation</h2>
<p>&nbsp;</p>
<p>I&rsquo;ll create an IItemContainer that has one property Container of type IDictionary. Then i&rsquo;ll create an HttpContextContainer that will implement this interface. This object will map the Container property to the HttpContext.Items property.</p>
<pre class="code"><span style="background: white; color: blue;">public interface </span><span style="background: white; color: #2b91af;">IItemContainer<br />    </span><span style="background: white; color: black;">{<br />        </span><span style="background: white; color: #2b91af;">IDictionary </span><span style="background: white; color: black;">Container { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; }<br />    }</span></pre>
<pre class="code"><span style="background: white; color: black;">[</span><span style="background: white; color: #2b91af;">Export</span><span style="background: white; color: black;">(</span><span style="background: white; color: blue;">typeof</span><span style="background: white; color: black;">(</span><span style="background: white; color: #2b91af;">IItemContainer</span><span style="background: white; color: black;">))]<br />    [</span><span style="background: white; color: #2b91af;">PartCreationPolicy</span><span style="background: white; color: black;">(</span><span style="background: white; color: #2b91af;">CreationPolicy</span><span style="background: white; color: black;">.Shared)]<br />    </span><span style="background: white; color: blue;">public class </span><span style="background: white; color: #2b91af;">HttpContextContainer </span><span style="background: white; color: black;">: MEF_Asp.Net_MVC_Per_Request.Models.</span><span style="background: white; color: #2b91af;">IItemContainer<br />    </span><span style="background: white; color: black;">{<br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">IDictionary </span><span style="background: white; color: black;">Container<br />        {<br />            </span><span style="background: white; color: blue;">get<br />            </span><span style="background: white; color: black;">{<br />                </span><span style="background: white; color: blue;">return </span><span style="background: white; color: #2b91af;">HttpContext</span><span style="background: white; color: black;">.Current.Items;<br />            }<br />        }       <br />    }</span></pre>
<p>&nbsp;</p>
<p>Next I'll create the proxy object (MyCustomObjectPerRequest). This object will contain the IItemContainer and the Exportfactory&lt;T&gt; instances and implement the IMyCustomObject. This object will be served by the MefContainer when something is request of type IMyCustomObject.</p>
<pre class="code"><span style="background: white; color: black;">[</span><span style="background: white; color: #2b91af;">Export</span><span style="background: white; color: black;">(</span><span style="background: white; color: blue;">typeof</span><span style="background: white; color: black;">(</span><span style="background: white; color: #2b91af;">IMyCustomObject</span><span style="background: white; color: black;">))]<br />    </span><span style="background: white; color: blue;">public class </span><span style="background: white; color: #2b91af;">MyCustomObjectPerRequest </span><span style="background: white; color: black;">: </span><span style="background: white; color: #2b91af;">IMyCustomObject<br />    </span><span style="background: white; color: black;">{<br />        [</span><span style="background: white; color: #2b91af;">Import</span><span style="background: white; color: black;">]<br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">ExportFactory</span><span style="background: white; color: black;">&lt;</span><span style="background: white; color: #2b91af;">MyCustomObject</span><span style="background: white; color: black;">&gt; Factory { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; </span><span style="background: white; color: blue;">set</span><span style="background: white; color: black;">; }<br /><br />        [</span><span style="background: white; color: #2b91af;">Import</span><span style="background: white; color: black;">]<br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">IItemContainer </span><span style="background: white; color: black;">ItemContainer { </span><span style="background: white; color: blue;">get</span><span style="background: white; color: black;">; </span><span style="background: white; color: blue;">set</span><span style="background: white; color: black;">; }<br /><br />        </span><span style="background: white; color: blue;">private </span><span style="background: white; color: #2b91af;">IMyCustomObject </span><span style="background: white; color: black;">MyCustomObject<br />        {<br />            </span><span style="background: white; color: blue;">get<br />            </span><span style="background: white; color: black;">{<br />                </span><span style="background: white; color: blue;">if </span><span style="background: white; color: black;">(</span><span style="background: white; color: blue;">null </span><span style="background: white; color: black;">== ItemContainer.Container[</span><span style="background: white; color: #a31515;">"MyCustomObject"</span><span style="background: white; color: black;">])<br />                {<br />                    ItemContainer.Container[</span><span style="background: white; color: #a31515;">"MyCustomObject"</span><span style="background: white; color: black;">] = Factory.CreateExport().Value;<br />                }<br /><br />                </span><span style="background: white; color: blue;">return </span><span style="background: white; color: black;">(</span><span style="background: white; color: #2b91af;">IMyCustomObject</span><span style="background: white; color: black;">)ItemContainer.Container[</span><span style="background: white; color: #a31515;">"MyCustomObject"</span><span style="background: white; color: black;">];<br />            }<br />        }<br /><br />        </span><span style="background: white; color: blue;">public </span><span style="background: white; color: #2b91af;">DateTime </span><span style="background: white; color: black;">Created<br />        {<br />            </span><span style="background: white; color: blue;">get<br />            </span><span style="background: white; color: black;">{<br />                </span><span style="background: white; color: blue;">return </span><span style="background: white; color: black;">MyCustomObject.Created;<br />            }<br />            </span><span style="background: white; color: blue;">set<br />            </span><span style="background: white; color: black;">{<br />                MyCustomObject.Created = </span><span style="background: white; color: blue;">value</span><span style="background: white; color: black;">;<br />            }<br />        }<br />    }</span></pre>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>We also need to change the ExportAttribute on the MyCustomObject class so that it doesn&rsquo;t specify a ContractType.</p>
<pre class="code"><span style="background: white; color: black;"> [</span><span style="background: white; color: #2b91af;">Export</span><span style="background: white; color: black;">] <br />    </span><span style="background: white; color: blue;">public class </span><span style="background: white; color: #2b91af;">MyCustomObject </span><span style="background: white; color: black;">: </span><span style="background: white; color: #2b91af;">IMyCustomObject</span></pre>
<p>&nbsp;</p>
<h2>The Result</h2>
<p>&nbsp;</p>
<p>When we know run the application, we will use the same MyCustomObject object per request. When we refresh, we&rsquo;ll get an other instance.</p>
<p>&nbsp;</p>
<p><a href="http://lh6.ggpht.com/-V1tlKyPGpUw/UNQjQX1EN4I/AAAAAAAAAKs/vDSXqR-cSCc/s1600-h/image%25255B21%25255D.png"><img style="display: block; float: none; margin-left: auto; margin-right: auto; border-width: 0px;" title="image" src="http://lh4.ggpht.com/-q1bHRWQRb3U/UNQjRD9nPtI/AAAAAAAAAK4/hVgumq4brY0/image_thumb%25255B13%25255D.png?imgmax=800" alt="image" width="793" height="118" border="0" /></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<h1>Conclusion</h1>
<p>&nbsp;</p>
<p>This solution can also be used to use the same instance per Session or to incorporate a caching mechanism.&nbsp; All that needs to been done is to implement the IItemsContainer and map the Container property. The Visual Studio solution can be downloaded <a href="http://dl.dropbox.com/u/41091233/Blog/MEF%20Asp.Net%20MVC%20Per%20Request/MEF%20Asp.Net%20MVC%20Per%20Request.rar" target="_blank">here</a>.</p>
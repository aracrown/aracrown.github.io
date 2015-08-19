---
layout: post
title: Java object to object mapping - which framework to choose? - Part 2
tags: java java-ee selma mapstruct mapping
author: vr
"header-img": "img/1.png"
published: true
---

<p>
We've started a discussion on current mapping frameworks in <a href="{{ site.baseurl }}/blog/Java-object-to-object-mapping-which-framework-to-choose/">Part 1</a>.
Selection was minimized to <a href="http://www.selma-java.org/" rel="nofollow">Selma</a> and <a href="http://mapstruct.org/" rel="nofollow">MapStruct</a>.
So which one to choose if I do not want to do the job myself?
</p>



<a href="#">
    <img class="img-responsive" src="{{ site.baseurl }}/img/object-mapping.jpg" alt="">
</a>
<span class="caption text-muted">Photographs by <a href="https://www.flickr.com/photos/bonaparty/">Louis K.</a>.</span>


<p>
Selma and <a href="http://mapstruct.org/" rel="nofollow">MapStruct</a> works well when initial learning curve passed how to configure a mapper. 
There might be the personal preference which one to choose. More customizations come with Mapstruct. Generated code might be more readable in <a href="http://mapstruct.org/" rel="nofollow">MapStruct</a>, however they both might generate duplicated code when mapping from Object to a simple entity in case Object has multiple properties mapped. 
</p>

<h3>Simple comparison</h3>

<table>
<thead>
<tr>
<th>Subject/Feature
</th>
<th>Selma
</th>
<th>MapStruct
</th>
</tr>
</thead>

<tr>
<td>Define mapping once
</td>
<td>ok
</td>
<td>using @InheritInverseConfiguration does not work in more complex scenarios (at the given time of writing the blog entry)
</td>
</tr>


<tr>
<td>Ease to define the mapping
</td>
<td>yes
</td>
<td>needs a good initial learning curve
</td>
</tr>


<tr>
<td>Customizations
</td>
<td>minimal
</td>
<td>advanced. Many places may be customized
</td>
</tr>

<tr>
<td>Generated code
</td>
<td>with duplications, readability might be improved
</td>
<td>with duplications, readability is better than Selma
</td>
</tr>

<tr>
<td>Annotation usage 
</td>
<td>class level
</td>
<td>method level
</td>
</tr>


</table>



<h3>Conclusion</h3>

<p>
I think both projects are great and it's up to you to decide which one you would like to use in your project. 
There is always another option - do it yourself :)
Currently I am using <a href="http://www.selma-java.org/" rel="nofollow">Selma</a>. Happy so far :)

</p>

<h3>Resources</h3>

<ul>
<li><a href="https://github.com/aracrown/ara-blog-examples/tree/master/s01e05" rel="nofollow">Source in Git repository</a></li>

</ul>
---
layout: post
title: Java object to object mapping - which framework to choose? - Part 1
tags: java java-ee selma mapstruct mapping
author: vr
"header-img": "img/1.png"
published: true
---

<p>
Many applications in my career had a need for a custom POJO mapper. Because of project complexity (and absence of existing mapping frameworks at that time) usually manual mapping was used in almost 99% cases :) What about another 1%? No mapping needed at all :)
</p>

<p>
I was following the discussions and mapping frameworks from time to time and was trying to figure out if I need one already? Are there any frameworks to do the job? Are there any frameworks to do the job right? Is it worth to look for such framework?
</p>

<a href="#">
    <img class="img-responsive" src="{{ site.baseurl }}/img/object-mapping.jpg" alt="">
</a>
<span class="caption text-muted">Photographs by <a href="https://www.flickr.com/photos/bonaparty/">Louis K.</a>.</span>



<p>
It depends. 
</p>

<ul>
<li>If your task is simple - then why another framework in your classpath?</li>
<li>If your objects are complex - will the framework do the job? You know the logic behind, so it might happen that you will complete the task faster than finding, configuring, running and testing a new framework;</li>
<li>For one-to-one mapping and cloning (same source type, same target type) maybe apache commons beanutils is enough?</li>
<li>Boring daily mapping? Yeah, maybe the mapper will help to make your life more interesting!</li>
</ul>

<h3>What are the requirements?</h3>

<p>
Let's make some requirements for a mapping framework. These will eliminate many of already existing  in the market.
</p>

<ul>
<li>DTO to JPA (or other DTO, does not matter too much) - meaning that JPA entity will not be exposed to a public world;</li>
<li>Compile time. Why? Do you really want a runtime bean resolving, reflection, etc? Then think about performance before choosing the right framework;</li>
<li>Annotation based. Ok, annotation based and compile time requirements are little bit too much, because annotation is used only to generate a code for you. But if it is easy to use - why not?</li>
</ul>


<h3>Mapping frameworks</h3>

<p>
There is a great article covering the existing frameworks: 
<a href="http://www.javacodegeeks.com/2013/10/java-object-to-object-mapper.html" rel="nofollow">Java Object to Object Mapper</a>
</p>

<p>
The majority of these frameworks do not have annotation based configuration except 
<a href="http://dozer.sourceforge.net/" rel="nofollow">Dozer</a>. However Dozer does not fly well according the performance comparision <a href="http://blog.sokolenko.me/2013/05/dozer-vs-orika-vs-manual.html" rel="nofollow">Dozer vs Orika vs Manual</a>. So I excluded this framework as well.
</p>

<p>
More interesting solutions are <a href="http://www.selma-java.org/" rel="nofollow">Selma</a> and <a href="http://mapstruct.org/" rel="nofollow">MapStruct</a>.
</p>

<p>
I will try to cover them in next post! 
</p>

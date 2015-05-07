---
layout: post
title: Java EE application configuration using Aeonbits Owner
category: configuration
tags: cdi java-ee owner configuration
author: vr
"header-img": "img/1.png"
published: true
---
<p>
Every (almost) Java EE based application needs configuration — local, test or production — it does not matter. What matters — how easily this configuration solution is maintained and how easily the environment may be changed.
</p>

<p>
There are many ways to achieve the requirement, however in this blog I will cover one of possible CDI based configuration solutions — Owner by <a href="http://owner.aeonbits.org/">http://owner.aeonbits.org/</a>.
</p>

<p >Usually there are several stages when properties used in production:</p>

<ol ><li >Hardcoded properties (usually done by novice programmers, otherwise I would treat such properties as constants);</li>
<li >Properties file inside application war file (different properties configured during build time. It is not correct, because the same build cannot be deployed on different environments);</li>
<li>Properties file stored in server file system (properties are independent from war and is environment specific).</li></ol>

<p name="f8b8" id="f8b8" class="graf--p">Java EE lacks easy configuration functionality. There are many discussions related to this topic (<a href="http://www.adam-bien.com/roller/abien/entry/how_to_configure_java_ee" data-href="http://www.adam-bien.com/roller/abien/entry/how_to_configure_java_ee" class="markup--anchor markup--p-anchor" rel="nofollow">HOW TO CONFIGURE JAVA EE 6+ APPLICATIONS&nbsp;…WITHOUT XML</a>, <a href="http://stackoverflow.com/questions/5335979/storing-and-editing-configuration-for-java-ee-applications" data-href="http://stackoverflow.com/questions/5335979/storing-and-editing-configuration-for-java-ee-applications" class="markup--anchor markup--p-anchor" rel="nofollow">Stack Overflow</a>,<a href="http://blog.eisele.net/2011/09/configure-java-ee-applications-or.html" data-href="http://blog.eisele.net/2011/09/configure-java-ee-applications-or.html" class="markup--anchor markup--p-anchor" rel="nofollow">Configure Java EE applications or “Putting Bien into practice”</a> and more others), however they need custom actions to take into account:</p>

<ul class="postList"><li name="09bc" id="09bc" class="graf--li">You need to manage property injection yourself;</li><li name="590b" id="590b" class="graf--li">You need to use <a href="http://projects.spring.io/spring-framework/" data-href="http://projects.spring.io/spring-framework/" class="markup--anchor markup--li-anchor" rel="nofollow">Spring framework</a>, which has flexible configuration management mechanism. But do you need <a href="http://projects.spring.io/spring-framework/" data-href="http://projects.spring.io/spring-framework/" class="markup--anchor markup--li-anchor" rel="nofollow">Spring</a> only for property loading? I believe — no ☺</li></ul>

<h3 name="0c34" id="0c34" class="graf--h3">Preconditions</h3>

<p name="5710" id="5710" class="graf--p">You need this environment setup in order to successfully complete the provided guide:</p>

<ul class="postList"><li name="415d" id="415d" class="graf--li"><a href="http://maven.apache.org/" data-href="http://maven.apache.org/" class="markup--anchor markup--li-anchor" rel="nofollow">Apache Maven 3.x</a></li><li name="7496" id="7496" class="graf--li"><a href="http://www.eclipse.org/downloads/" data-href="http://www.eclipse.org/downloads/" class="markup--anchor markup--li-anchor" rel="nofollow">Eclipse Luna</a></li><li name="e391" id="e391" class="graf--li">Java EE 7 profile</li><li name="4086" id="4086" class="graf--li"><a href="http://owner.aeonbits.org/" data-href="http://owner.aeonbits.org/" class="markup--anchor markup--li-anchor" rel="nofollow"><strong class="markup--strong markup--li-strong">Aeonbits Owner</strong></a></li></ul>

<h3>Implementation</h3>

<p name="a2da" id="a2da" class="graf--p"><strong class="markup--strong markup--p-strong">Defining an interface</strong></p>

<pre name="f10c" id="f10c" class="graf--pre">/**<br> * Application configuration properties are defined here.<br> * <br> * <a href="http://twitter.com/author" data-href="http://twitter.com/author" class="markup--anchor markup--pre-anchor" title="Twitter profile for @author" rel="nofollow">@author</a> vicento.ramos<br> *<br> */<br><a href="http://twitter.com/HotReload" data-href="http://twitter.com/HotReload" class="markup--anchor markup--pre-anchor" title="Twitter profile for @HotReload" rel="nofollow">@HotReload</a><br><a href="http://twitter.com/Sources" data-href="http://twitter.com/Sources" class="markup--anchor markup--pre-anchor" title="Twitter profile for @Sources" rel="nofollow">@Sources</a>({ “file:/etc/my-app.properties”, “file:~/.my-app.properties”, “classpath:/my-app.properties” })<br><a href="http://twitter.com/Named" data-href="http://twitter.com/Named" class="markup--anchor markup--pre-anchor" title="Twitter profile for @Named" rel="nofollow">@Named</a>(“applicationConfig”)<br>public interface ApplicationConfig extends Accessible {<br> <a href="http://twitter.com/DefaultValue" data-href="http://twitter.com/DefaultValue" class="markup--anchor markup--pre-anchor" title="Twitter profile for @DefaultValue" rel="nofollow">@DefaultValue</a>(“50”)<br> <a href="http://twitter.com/Key" data-href="http://twitter.com/Key" class="markup--anchor markup--pre-anchor" title="Twitter profile for @Key" rel="nofollow">@Key</a>(“app.timeout”)<br> int getInitialTimeout();<br>}</pre>

<p name="bc75" id="bc75" class="graf--p"><strong class="markup--strong markup--p-strong">Creating a producer</strong></p>

<pre name="9ff1" id="9ff1" class="graf--pre">/**<br> * Application scope based configuration file producer.<br> * <br> * <a href="http://twitter.com/author" data-href="http://twitter.com/author" class="markup--anchor markup--pre-anchor" title="Twitter profile for @author" rel="nofollow">@author</a> vicento.ramos<br> *<br> */<br><a href="http://twitter.com/ApplicationScoped" data-href="http://twitter.com/ApplicationScoped" class="markup--anchor markup--pre-anchor" title="Twitter profile for @ApplicationScoped" rel="nofollow">@ApplicationScoped</a><br>public class ApplicationConfigProducer {<br> <a href="http://twitter.com/Produces" data-href="http://twitter.com/Produces" class="markup--anchor markup--pre-anchor" title="Twitter profile for @Produces" rel="nofollow">@Produces</a><br> <a href="http://twitter.com/ApplicationScoped" data-href="http://twitter.com/ApplicationScoped" class="markup--anchor markup--pre-anchor" title="Twitter profile for @ApplicationScoped" rel="nofollow">@ApplicationScoped</a><br> public ApplicationConfig produceSportsConfig() {<br> return ConfigFactory.create(ApplicationConfig.class);<br> }<br>}</pre>

<p name="220a" id="220a" class="graf--p"><strong class="markup--strong markup--p-strong">Creating a properties file</strong></p>

<p name="fb3a" id="fb3a" class="graf--p"><strong class="markup--strong markup--p-strong">my-app.properties </strong>file may be stored in one of these locations:</p>

<ul class="postList"><li name="0689" id="0689" class="graf--li">/etc/my-app.properties</li><li name="7b13" id="7b13" class="graf--li">.my-app.properties</li><li name="b1cd" id="b1cd" class="graf--li">/my-app.properties</li></ul>

<pre name="09f8" id="09f8" class="graf--pre">app.timeout=30</pre>

<p name="d030" id="d030" class="graf--p"><strong class="markup--strong markup--p-strong">Usage</strong></p>

<pre name="2797" id="2797" class="graf--pre">/**<br> * Sample injection point of properties.<br> * <br> * <a href="http://twitter.com/author" data-href="http://twitter.com/author" class="markup--anchor markup--pre-anchor" title="Twitter profile for @author" rel="nofollow">@author</a> vicento.ramos<br> *<br> */<br><a href="http://twitter.com/Named" data-href="http://twitter.com/Named" class="markup--anchor markup--pre-anchor" title="Twitter profile for @Named" rel="nofollow">@Named</a><br>public class SampleService {<br> <a href="http://twitter.com/Inject" data-href="http://twitter.com/Inject" class="markup--anchor markup--pre-anchor" title="Twitter profile for @Inject" rel="nofollow">@Inject</a><br> private ApplicationConfig applicationConfig;<br> <br> public void method() {<br> System.out.println(String.format(“Config value: %d”, applicationConfig.getInitialTimeout()));<br> }<br>}</pre>

<h3 name="1b2b" id="1b2b" class="graf--h3">Features</h3>

<ul class="postList"><li name="911f" id="911f" class="graf--li">Multiple configuration locations — you may configure multiple lookup locations where configuration files may be fetched from;</li><li name="e12d" id="e12d" class="graf--li">CDI Injection — Java EE based CDI injection using <em class="markup--em markup--li-em">@Named</em> and <em class="markup--em markup--li-em">@Inject </em>annotation</li><li name="7db0" id="7db0" class="graf--li">Default values — you may configure default values easily using <em class="markup--em markup--li-em">@DefaultValue</em></li><li name="5f93" id="5f93" class="graf--li">Hot reload — you may easily define the interval for properties hot reloading using <em class="markup--em markup--li-em">@HotReload</em></li><li name="e1da" id="e1da" class="graf--li">And many more if required</li></ul>

<h3 name="4f38" id="4f38" class="graf--h3">Resources</h3>

<ul class="postList"><li name="2a0e" id="2a0e" class="graf--li graf--last"><a href="https://github.com/aracrown/ara-blog-examples/tree/master/ch01" data-href="https://github.com/aracrown/ara-blog-examples/tree/master/ch01" class="markup--anchor markup--li-anchor" rel="nofollow">Source in Git repository</a></li></ul>

<p>
</p>


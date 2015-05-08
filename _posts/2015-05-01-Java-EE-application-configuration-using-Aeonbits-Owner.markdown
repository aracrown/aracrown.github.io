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
Every (almost) Java EE based application needs configuration — local, test or production — it does not matter. What matters — how easily this
configuration solution is maintained and how easily the environment may be changed. 
</p>

<p> 
There are many ways to achieve the requirement, however in this blog I will cover one of possible CDI based configuration solutions — Owner by <a
href="http://owner.aeonbits.org/">http://owner.aeonbits.org/</a>. 
</p>

<p>
Usually there are several stages when properties used in production:
</p>

<ol>
<li >
Hardcoded properties (usually done by novice programmers, otherwise I would treat such properties as constants);</li> <li >Properties file
inside application war file (different properties configured during build time. It is not correct, because the same build cannot be deployed on
different environments);
</li> 
<li>
Properties file stored in server file system (properties are independent from war and is environment
specific).
</li>
</ol>

<p>
Java EE lacks easy configuration functionality. There are many discussions related to this topic
</p>

<ol>
<li>
<a
href="http://www.adam-bien.com/roller/abien/entry/how_to_configure_java_ee"
data-href="http://www.adam-bien.com/roller/abien/entry/how_to_configure_java_ee" rel="nofollow">HOW TO
CONFIGURE JAVA EE 6+ APPLICATIONS&nbsp;…WITHOUT XML</a>
</li>

<li>
<a
href="http://stackoverflow.com/questions/5335979/storing-and-editing-configuration-for-java-ee-applications"
data-href="http://stackoverflow.com/questions/5335979/storing-and-editing-configuration-for-java-ee-applications" rel="nofollow">Storing and editing configuration for Java EE applications at Stack Overflow</a>
</li>
<li>
<a href="http://blog.eisele.net/2011/09/configure-java-ee-applications-or.html"
data-href="http://blog.eisele.net/2011/09/configure-java-ee-applications-or.html" rel="nofollow">Configure
Java EE applications or “Putting Bien into practice”</a> and more others.
</li>
</ol>

<p>
However they need custom actions to take into account:
</p>

<ul>
<li>
You need to manage property injection yourself;
</li>
<li>
You need to use <a href="http://projects.spring.io/spring-framework/" data-href="http://projects.spring.io/spring-framework/" 
rel="nofollow">Spring framework</a>, which has flexible configuration management mechanism. But do you need
<a href="http://projects.spring.io/spring-framework/" data-href="http://projects.spring.io/spring-framework/"
rel="nofollow">Spring</a> only for property loading? I believe — no ☺
</li>
</ul>

<h3>Preconditions</h3>

<p>You need this environment setup in order to successfully complete the provided guide:</p>

<ul>
<li>
<a href="http://maven.apache.org/" data-href="http://maven.apache.org/" rel="nofollow">Apache Maven 3.x</a>
</li>
<li>
<a href="http://www.eclipse.org/downloads/" data-href="http://www.eclipse.org/downloads/" rel="nofollow">Eclipse
Luna</a>
</li>
<li>
Java EE 7 profile
</li>
<li>
<a href="http://owner.aeonbits.org/" data-href="http://owner.aeonbits.org/" rel="nofollow"><strong>Aeonbits Owner</strong></a>
</li>
</ul>

<h3>Implementation</h3>

<p><strong >Defining an interface</strong></p>

<pre>
/**<br> * Application configuration properties are defined here.<br> * <br> * <a
href="http://twitter.com/author" data-href="http://twitter.com/author"  title="Twitter profile for @author"
rel="nofollow">@author</a> vicento.ramos<br> *<br> */<br><a href="http://twitter.com/HotReload" data-href="http://twitter.com/HotReload"
 title="Twitter profile for @HotReload" rel="nofollow">@HotReload</a><br><a href="http://twitter.com/Sources"
data-href="http://twitter.com/Sources"  title="Twitter profile for @Sources" rel="nofollow">@Sources</a>({
“file:/etc/my-app.properties”, “file:~/.my-app.properties”, “classpath:/my-app.properties” })<br><a href="http://twitter.com/Named"
data-href="http://twitter.com/Named"  title="Twitter profile for @Named"
rel="nofollow">@Named</a>(“applicationConfig”)<br>public interface ApplicationConfig extends Accessible {<br> <a
href="http://twitter.com/DefaultValue" data-href="http://twitter.com/DefaultValue"  title="Twitter profile
for @DefaultValue" rel="nofollow">@DefaultValue</a>(“50”)<br> <a href="http://twitter.com/Key" data-href="http://twitter.com/Key"
 title="Twitter profile for @Key" rel="nofollow">@Key</a>(“app.timeout”)<br> int
getInitialTimeout();<br>}
</pre>

<p><strong>Creating a producer</strong></p>

<pre>
/**<br> * Application scope based configuration file producer.<br> * <br> * <a
href="http://twitter.com/author" data-href="http://twitter.com/author"  title="Twitter profile for @author"
rel="nofollow">@author</a> vicento.ramos<br> *<br> */<br><a href="http://twitter.com/ApplicationScoped"
data-href="http://twitter.com/ApplicationScoped"  title="Twitter profile for @ApplicationScoped"
rel="nofollow">@ApplicationScoped</a><br>public class ApplicationConfigProducer {<br> <a href="http://twitter.com/Produces"
data-href="http://twitter.com/Produces"  title="Twitter profile for @Produces"
rel="nofollow">@Produces</a><br> <a href="http://twitter.com/ApplicationScoped" data-href="http://twitter.com/ApplicationScoped" title="Twitter profile for @ApplicationScoped" rel="nofollow">@ApplicationScoped</a><br> public ApplicationConfig
produceSportsConfig() {<br> return ConfigFactory.create(ApplicationConfig.class);<br> }<br>}
</pre>

<p><strong>Creating a properties file</strong></p>

<p><strong>my-app.properties </strong>file may be stored in one of these
locations:</p>

<ul>
<li>/etc/my-app.properties</li>
<li>.my-app.properties</li>
<li>/my-app.properties</li>
</ul>

<p>
And the content of the file:</p>

<pre>app.timeout=30</pre>

<p><strong>Usage</strong></p>

<pre>/**<br> * Sample injection point of properties.<br> * <br> * <a href="http://twitter.com/author"
data-href="http://twitter.com/author"  title="Twitter profile for @author" rel="nofollow">@author</a>
vicento.ramos<br> *<br> */<br><a href="http://twitter.com/Named" data-href="http://twitter.com/Named" 
title="Twitter profile for @Named" rel="nofollow">@Named</a><br>public class SampleService {<br> <a href="http://twitter.com/Inject"
data-href="http://twitter.com/Inject"  title="Twitter profile for @Inject" rel="nofollow">@Inject</a><br>
private ApplicationConfig applicationConfig;<br> <br> public void method() {<br> System.out.println(String.format(“Config value: %d”,
applicationConfig.getInitialTimeout()));<br> }<br>}</pre>

<h3>Features</h3>

<ul>
<li>Multiple configuration locations — you may configure multiple lookup locations where
configuration files may be fetched from;</li>
<li>CDI Injection — Java EE based CDI injection using <em>@Named</em> and <em>@Inject </em>annotation</li><li>Default values — you may configure default values easily using <em>@DefaultValue</em></li><li>Hot reload — you may easily define the interval for properties hot reloading using 
<em>@HotReload</em></li><li>And many more if required</li></ul>

<h3>Resources</h3>

<ul><li><a href="https://github.com/aracrown/ara-blog-examples/tree/master/s01e01"
data-href="https://github.com/aracrown/ara-blog-examples/tree/master/s01e01" rel="nofollow">Source in Git
repository</a></li></ul>


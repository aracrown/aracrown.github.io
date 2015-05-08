---
layout: post
title: Web application environment configuration using maven profiles
category: configuration
tags: java-ee configuration maven CD
author: vr
"header-img": "img/1.png"
published: true
---

<p>
Ok, this bog will be short. I will just share some small maven code snippets to configure your build and development IDE environment.

</p>

<p>
For some projects there is a need to use specific environment properties, which may differ from deployment environment.
Let's say in your IDE you use <em>"Development"</em> mode, however it is obvious that test, QA and production environments will use <em>"Deployment"</em> mode.
</p>
<p>
Usually such property is stored in <strong>web.xml</strong> file, for example:
</p>

<pre>
&lt;context-param&gt;
    &lt;param-name&gt;javax.faces.PROJECT_STAGE&lt;/param-name&gt;
    &lt;param-value&gt;${jsf.projectStage}&lt;/param-value&gt;
&lt;/context-param&gt;
</pre>

<h3>Issue</h3>
<ol>
<li>
We do not want to commit <em>"Development"</em> mode into source control.
</li>
<li>
We do not want to use <em>"Deployment"</em> mode in our local IDE.
</li>
<li>
We do not want to have uncommitted changes if using <em>"Development"</em> mode locally.
</li>
<li>
We DO want properly running local environment :) Sure!
</li>
</ol>

<p>
By default property <em>${jsf.projectStage}</em> is not properly understood by IDE (at least Eclipse WTP) and is not transformed into real value. Well, maybe it should be a nice feature...
However, if you are using <a href="http://maven.apache.org/" data-href="http://maven.apache.org/" rel="nofollow">Apache Maven</a> then this can be easily fixed.
</p>

<h3>Talks and documentation</h3>

<ol>
<li>
<a href="http://maven.apache.org/guides/introduction/introduction-to-profiles.html" rel="nofollow">
Introduction to Build Profiles
</a>
</li>

<li>
<a href="http://stackoverflow.com/questions/28861267/eclipse-maven-profile-ignored" rel="nofollow">
Eclipse + maven : profile ignored 
</a>
</li>
<li>
And the solution is on <a href="http://stackoverflow.com/" rel="nofollow">stackoverflow</a> described as well, however i could not find the link to it anymore when writing this blog :) I hope you will share this link with me :)
</li>
</ol>


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
</ul>

<h3>Solution</h3>



<p>
So far everything is about your <strong>pom.xml</strong> file configuration.
You need to tell maven what resources should be filtered. Also tell maven which property file to use for resource filtering. 
And resolve this property file name dynamically using <em>filter.name</em> maven property.
</p>

<pre>
&lt;build&gt;
    &lt;!-- Add filter file name --&gt;
    &lt;filters&gt;
        &lt;filter&gt;${basedir}/src/main/filters/${filter.name}.properties&lt;/filter&gt;
    &lt;/filters&gt;

    &lt;plugins&gt;
        &lt;!--  Define filtered resources --&gt;
        &lt;plugin&gt;
            &lt;artifactId&gt;maven-war-plugin&lt;/artifactId&gt;
            &lt;version&gt;2.1.1&lt;/version&gt;
            &lt;configuration&gt;
                &lt;failOnMissingWebXml&gt;false&lt;/failOnMissingWebXml&gt;
                &lt;webResources&gt;
                    &lt;resource&gt;
                        &lt;directory&gt;${basedir}/src/main/webapp&lt;/directory&gt;
                        &lt;filtering&gt;true&lt;/filtering&gt;
                        &lt;includes&gt;
                            &lt;include&gt;WEB-INF/web.xml&lt;/include&gt;
                            &lt;include&gt;WEB-INF/jboss-web.xml&lt;/include&gt;
                        &lt;/includes&gt;
                    &lt;/resource&gt;
                &lt;/webResources&gt;
            &lt;/configuration&gt;
        &lt;/plugin&gt;
    &lt;/plugins&gt;
&lt;/build&gt;
</pre>

<p>
Then define different profiles you will need. And tell each profile which property file should be applied (<strong>&lt;filter.name&gt;production&lt;/filter.name&gt;</strong>).
</p>

<pre>
&lt;profiles&gt;
    &lt;profile&gt;
        &lt;id&gt;default&lt;/id&gt;
        &lt;activation&gt;
            &lt;activeByDefault&gt;true&lt;/activeByDefault&gt;
        &lt;/activation&gt;
    &lt;/profile&gt;
    &lt;profile&gt;
        &lt;id&gt;production&lt;/id&gt;
        &lt;properties&gt;
            &lt;filter.name&gt;production&lt;/filter.name&gt;
        &lt;/properties&gt;
    &lt;/profile&gt;
&lt;/profiles&gt;
</pre>

<p>
But we would like to have a default configuration for local environment. Let's define it in <strong>pom.xml</strong> as well.
</p>

<pre>
&lt;properties&gt;
    &lt;!-- Default name of file for filtering --&gt;
    &lt;filter.name&gt;localhost&lt;/filter.name&gt;
&lt;/properties&gt;</pre>


<p><strong>Usage</strong></p>

<p>
Now, eclipse IDE should automatically resolve and use default properties when you import maven project.
For continuous delivery and console based builds you might be using such command:
</p>

<pre>
mvn clean package -Pproduction
</pre>

<p>
<b>-P</b> may be avoided if you want to use default properties, e.g. for development build.
</p>

<p><strong>Properties file location</strong></p>

<p>
Yes, you may define your own property files location based on your project structure:
</p>

<pre>
&lt;filters&gt;
 &lt;filter&gt;${basedir}/src/main/filters/${filter.name}.properties&lt;/filter&gt;
&lt;/filters&gt;
</pre>





<h3>Resources</h3>

<ul><li>
<a href="https://github.com/aracrown/ara-blog-examples/tree/master/s01e02" rel="nofollow">Source in Git
repository</a>

</li></ul>


<p>
Hopefully you did enjoy reading!
</p>

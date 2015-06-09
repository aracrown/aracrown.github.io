---
layout: post
title: Micro Java EE application with Wildfly Swarm
tags: java-ee wildfly swarm microservices REST
author: vr
"header-img": "img/1.png"
published: true
---

<p>
Microservices are here already for a while. They came with cloud solutions, RESTful services, <a href="http://backbonejs.org/" rel="nofollow">Backbone.js</a> , 
<a href="https://angularjs.org/" rel="nofollow">AngularJS</a>  and other javascript frameworks, however only recently they got a new keyword - "microservices".
And of course they will stay. Due to the nature of nowadays software architecture, cloud hosting and client rendering.
I you want to be pragmatic and lean - you do not need microservices. If your software is complex, it needs easy support, fast deployment, easy clustering and serving for a single module (e.g. finance module within huge
CRM) then you might thing about going into microservices.
And here <a href="http://wildfly.org/news/2015/06/05/WildFly-Swarm-Alpha2/" rel="nofollow">Wildfly Swarm</a> comes in.

</p>

<h3>What is Wildfly Swarm?</h3>


<p>
You can think about <a href="http://wildfly.org/news/2015/06/05/WildFly-Swarm-Alpha2/" rel="nofollow">Wildfly Swarm</a>  as a micro application server with features which are required for your applications only.
And these features are embedded into one single jar together with your module. Yes, coupling with application server does not smell well, but useful in some cases. That's why I 
always say that you need to stick with your requirements and check if need such type of architecture.

</p>

<h3>Example</h3>

<p>
The easiest example to provide is REST + DB as a single microservice for a single module. You define data entities and expose them via REST endpoint. UI may be created as a separate microservice.
</p>

<h3>What you need?</h3>

<p>
Wildfly Swarm Alpha3-SNAPSHOT. Alpha1 did not support JPA and Alpha2 still has some issues which are already fixed in latest SNAPSHOT version. This means that you need to grab it from github and build yourself. 
Or wait for Alpha3 release.
</p>

<p>
Then you need some wildfly dependencies.
</p>

<pre>
&lt;dependency&gt;
	&lt;groupId&gt;org.wildfly.swarm&lt;/groupId&gt;
	&lt;artifactId&gt;wildfly-swarm-jpa&lt;/artifactId&gt;
	&lt;version&gt;${wildfly-swarm.version}&lt;/version&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.wildfly.swarm&lt;/groupId&gt;
	&lt;artifactId&gt;wildfly-swarm-weld-jaxrs&lt;/artifactId&gt;
	&lt;version&gt;${wildfly-swarm.version}&lt;/version&gt;
&lt;/dependency&gt;
</pre>

<p>
And wildfly plugins...
</p>
<pre>
&lt;plugin&gt;
	&lt;groupId&gt;org.wildfly.swarm&lt;/groupId&gt;
	&lt;artifactId&gt;wildfly-swarm-plugin&lt;/artifactId&gt;
	&lt;version&gt;${wildfly-swarm.version}&lt;/version&gt;
	&lt;configuration&gt;
		&lt;mainClass&gt;org.aracrown.blog.s01e04.Main&lt;/mainClass&gt;
	&lt;/configuration&gt;
	&lt;executions&gt;
		&lt;execution&gt;
			&lt;goals&gt;
				&lt;goal&gt;package&lt;/goal&gt;
			&lt;/goals&gt;
		&lt;/execution&gt;
	&lt;/executions&gt;
&lt;/plugin&gt;
</pre>

<p>
And some code... Ok, <a href="https://github.com/aracrown/ara-blog-examples/tree/master/s01e04" rel="nofollow">source is in Git repository</a>, I will not repeat myself here :)
</p>


<p>
And the main thing, which comes to Wildfly swarm - configuration! So far you need a Java class with main method in order to be able to start it as a regular jar archive. That's the place where nasty things happen :)

</p>
<pre>
package org.aracrown.blog.s01e04;

import org.aracrown.blog.s01e04.dao.BlogEntryDao;
import org.aracrown.blog.s01e04.model.BlogEntry;
import org.aracrown.blog.s01e04.query.BlogEntryQueryImpl;
import org.aracrown.blog.s01e04.resources.BlogEntriesResource;
import org.jboss.shrinkwrap.api.asset.ClassLoaderAsset;
import org.wildfly.swarm.container.Container;
import org.wildfly.swarm.datasources.Datasource;
import org.wildfly.swarm.datasources.DatasourceDeployment;
import org.wildfly.swarm.datasources.DriverDeployment;
import org.wildfly.swarm.jaxrs.JAXRSDeployment;

public class Main {
	public static void main(String[] args) throws Exception {
		Container container = new Container();
		container.start();

		DriverDeployment driverDeployment = new DriverDeployment(container, "com.h2database:h2", "h2");
		container.deploy(driverDeployment);

		// Create a DS deployment
		DatasourceDeployment dsDeployment = new DatasourceDeployment(container, new Datasource("ExampleDS")
				.connectionURL("jdbc:h2:mem:test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE").driver("h2").authentication("sa", "sa"));
		container.deploy(dsDeployment);

		JAXRSDeployment deployment = new JAXRSDeployment(container);
		deployment.getArchive().addClasses(BlogEntryDao.class, BlogEntryQueryImpl.class);
		deployment.getArchive().addPackage(BlogEntry.class.getPackage());
		deployment.getArchive().addPackage(BlogEntriesResource.class.getPackage());
		deployment.getArchive().addAsWebInfResource(new ClassLoaderAsset("META-INF/persistence.xml", Main.class.getClassLoader()),
				"classes/META-INF/persistence.xml");
		deployment.getArchive().addAsWebInfResource(new ClassLoaderAsset("META-INF/load.sql", Main.class.getClassLoader()),
				"classes/META-INF/load.sql");
		deployment.getArchive().addAsWebInfResource(new ClassLoaderAsset("META-INF/beans.xml", Main.class.getClassLoader()),
				"classes/META-INF/beans.xml");

		container.deploy(deployment);
	}
}

</pre>

<p>
You tell the container to use JAX-RS and datasource modules only. You can expose your configuration properties to external properties file, but what you need is adding classes and resources into archive manually.
Executing maven goal <strong>mvn package</strong> will create you a single jar containing all required dependencies with a suffix <strong>"-swarm"</strong>.
</p>

<p>
And at the end you have a single jar file and you can distribute it onto as many servers as you need. and run with just a single line: 
</p>

<pre>java -jar target/s01e04-1.0.0-SNAPSHOT-swarm.jar</pre>

<h3>Resources</h3>

<ul>
<li><a href="https://github.com/aracrown/ara-blog-examples/tree/master/s01e04" rel="nofollow">Source in Git repository</a></li>
<li><a href="https://github.com/wildfly-swarm/wildfly-swarm-examples" rel="nofollow">Wildfly-Swarm examples privided by Wildfly</a></li>

</ul>

<h3>Talks and discussions</h3>

<ul>
<li><a href="https://www.chrisstucchio.com/blog/2014/microservices_for_the_grumpy_neckbeard.html" rel="nofollow">Microservices for the Grumpy Neckbeard</a></li>
<li><a href="http://www.boundary.com/blog/2014/08/microservices-conways-law/" rel="nofollow">Microservices, or How I Learned To Stop Making Monoliths and Love Conway’s Law</a></li>
<li><a href="http://rick-hightower.blogspot.fi/2015/03/java-microservices-architecture.html" rel="nofollow">Java Microservices Architecture</a></li>
<li><a href="http://martinfowler.com/bliki/MonolithFirst.html" rel="nofollow">MonolithFirst</a></li>
<li><a href="http://bravenewgeek.com/service-disoriented-architecture/" rel="nofollow">Service-Disoriented Architecture</a></li>

</ul>

<p>
Happy designing and coding! 
</p>

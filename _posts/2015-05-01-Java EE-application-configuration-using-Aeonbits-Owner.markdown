---
layout: post
title: Java EE application configuration using Aeonbits Owner
subtitle: null
date: {}
author: vr
"header-img": "img/post-bg-01.jpg"
published: true
---

<p>Not completed yet! ☺</p>


<p>Every (almost) Java EE based application needs configuration — local, test or production — it does not matter. What matters — how easily this configuration solution is maintained and how easily the environment may be changed.</p>

<p>There are many ways to achieve the requirement, however in this blog I will cover one of possible CDI based configuration solutions — Owner by http://owner.aeonbits.org/.</p>

<p>Usually there are several stages when properties used in production:</p>

<p>Hardcoded properties (usually done by novice programmers, otherwise I would treat such properties as constants);</p>
<p>Properties file inside application war file (different properties configured during build time. It is not correct, because the same build cannot be deployed on different environments);</p>
<p>Properties file stored in server file system (properties are independent from war and is environment specific).</p>
<p>Java EE lacks easy configuration functionality. There are many discussions related to this topic ([HOW TO CONFIGURE JAVA EE 6+ APPLICATIONS …WITHOUT XML](www.lrytas.lt), Stack Overflow,Configure Java EE applications or “Putting Bien into practice” and more others), however they need custom actions to take into account:</p>

<p>You need to manage property injection yourself;</p>
<p>You need to use Spring framework, which has flexible configuration management mechanism. But do you need Spring only for property loading? I believe — no ☺</p>
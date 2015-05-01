---
layout: post
title: Java EE application configuration using Aeonbits Owner
subtitle: because they lacked opposable thumbs and the brainpower to build a space program.
date: {}
author: vr
"header-img": "img/post-bg-01.jpg"
published: true
---

Every (almost) Java EE based application needs configuration — local, test or production — it does not matter. What matters — how easily this configuration solution is maintained and how easily the environment may be changed.

There are many ways to achieve the requirement, however in this blog I will cover one of possible CDI based configuration solutions — Owner by http://owner.aeonbits.org/.

Usually there are several stages when properties used in production:

Hardcoded properties (usually done by novice programmers, otherwise I would treat such properties as constants);
Properties file inside application war file (different properties configured during build time. It is not correct, because the same build cannot be deployed on different environments);
Properties file stored in server file system (properties are independent from war and is environment specific).
Java EE lacks easy configuration functionality. There are many discussions related to this topic (HOW TO CONFIGURE JAVA EE 6+ APPLICATIONS …WITHOUT XML, Stack Overflow,Configure Java EE applications or “Putting Bien into practice” and more others), however they need custom actions to take into account:

You need to manage property injection yourself;
You need to use Spring framework, which has flexible configuration management mechanism. But do you need Spring only for property loading? I believe — no ☺
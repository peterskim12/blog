---
layout: post
title: Roxy Framework app-type
date: '2013-11-05T16:06:00-05:00'
tags:
- MarkLogic
- roxy
tumblr_url: http://peterskim.tumblr.com/post/66119736981/roxy-framework-app-type
---
The Roxy Framework for MarkLogic lets you choose between one of three app-types when you first instantiate the application:

* mvc: a normal, XQuery-based Roxy MVC app
* rest: an app based on the ML REST API
* hybrid: an app that uses Roxy rewriting and the ML REST API

This can be a confusing choice but after a few months of using Roxy for a number of different use cases, I’ve come to the conclusion that most people will want to choose either the ‘mvc’ or ‘hybrid’ app-type. 

*Which one should you choose?*

If you know with absolute certainty that your application will be written in XQuery, you want to leverage the Roxy MVC framework and will not be using the MarkLogic REST API at all, then ‘mvc’ is the clear choice. This is the traditional MarkLogic application development model that many experienced MarkLogic developers prefer over the new REST and Java APIs.

If you think you’ll be using the ML REST or Java API, whether it is from a front-end application also hosted from the MarkLogic HTTP application server or a separate Java/Ruby/Python/.NET application server, you should choose the ‘hybrid’ app type.

What is the difference between the ‘hybrid’ and ‘rest’ app type? The primary difference is the URL rewriter that’s used to route requests to the application server. The ‘rest’ app type configures the URL rewriter to be the default MarkLogic REST API rewriter which only really knows how to handle requests to the known REST API endpoints. The ‘hybrid’ app type configures the URL rewriter to be the Roxy rewriter which by default, knows how to route requests to the MarkLogic REST API endpoints but also lets you add additional routes so you can host additional endpoints from the same app server instance.

Why would you want to host additional endpoints from the same app server instance? You may want to develop custom endpoints that perform application-specific logic which would otherwise require multiple queries if you solely relied on the out of the box ML REST API. Yes, the MarkLogic REST API does provide an extension mechanism to support custom endpoints but I’ve found that utilizing the Roxy rewriter’s ability to route REST API and custom endpoint requests to be a better/cleaner/more flexible way to provide custom services. 

In summary, my recommendations are:

1) If you are writing an XQuery application with absolutely no intention of using the REST API, choose the ‘mvc’ app-type.

2) Otherwise, choose the ‘hybrid’ app-type and consider writing custom endpoints as Roxy application endpoints instead of REST API extensions.

Update (11/6/2013): My colleague Dave Cassel brought up a good point with me that the MarkLogic Java API provides a mechanism to call REST API extensions in the API itself: http://docs.marklogic.com/guide/java/resourceservices#chapter. However, if we’re assuming we’re working in a Java context anyways, it’s also worth considering the fact that many modern Java application frameworks provide helper utilities and constructs for calling RESTful web services. For example, Spring provides a RestTemplate as documented here: http://spring.io/guides/gs/consuming-rest/. Play Framework provides the play.libs.WS library as documented here: http://www.playframework.com/documentation/2.2.x/JavaWS. While it’s nice that ML REST API extensions can be exposed through the ML Java API itself, it’s clear that implementing this custom logic via Roxy endpoints would not introduce much more complexity or additional work.

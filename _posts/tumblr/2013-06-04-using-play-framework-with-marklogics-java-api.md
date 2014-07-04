---
layout: post
title: Using Play Framework with MarkLogic's Java API
date: '2013-06-04T20:59:00-04:00'
tags:
- MarkLogic
tumblr_url: http://peterskim.tumblr.com/post/52182362495/using-play-framework-with-marklogics-java-api
---
My colleague, Adam Fowler, just posted a great blog entry about his experience building user interfaces with the REST API that was introduced in MarkLogic 6:

[http://adamfowlerml.wordpress.com/2013/06/03/what-web-widgets-are-now-available-for-marklogic/](http://adamfowlerml.wordpress.com/2013/06/03/what-web-widgets-are-now-available-for-marklogic/)

I found his MLDB JavaScript API particularly interesting:

[https://github.com/adamfowleruk/mldb](https://github.com/adamfowleruk/mldb)

This library provides a convenient JavaScript wrapper for common operations on MarkLogic V6’s REST API. It abstracts authentication and common search settings to make it intuitive for a non MarkLogician to get started rapidly with MarkLogic.

While Adam has been working on simplifying MarkLogic UI development with MLDB, I’ve been thinking about using some Java framework to act as an additional application tier in between the MarkLogic REST API and the browser. Call me old-school, but I’m still uncomfortable with the idea of having client-side code in the browser connect to my data source directly. I think there are still some advantages to adding an additional tier of abstraction in between the data source (in this case, a MarkLogic server accessible via REST/Java API) and the browser:

* Security
* Optimization of the size of model objects to minimize the payload sent to the browser
* Optimization of the format of model objects to simplify parsing data in the browser code
* Access to a broader ecosystem of third-party libraries and services

While I’ve had more experience using Spring Framework over other Java-based MVC frameworks, I wanted an opportunity to try building something with Play Framework:

[http://www.playframework.com/](http://www.playframework.com/)

It seems to be a more lightweight framework than Spring… more like Ruby on Rails with its emphasis on convention over configuration and integrated unit testing. It’s still Java though, so you can leverage the whole ecosystem of libraries in the Java world.

The documentation for getting started with Play Framework is extremely straight-forward and it’d be a waste of effort to rehash that so I’ll direct you to that tutorial:

[http://www.playframework.com/documentation/2.1.1/Home](http://www.playframework.com/documentation/2.1.1/Home)

In my brief experience working with Play Framework so far, some of the features I find specifically helpful for MarkLogic application development are:

* Ease and flexibility of defining routes for HTTP requests: [http://www.playframework.com/documentation/2.1.1/JavaRouting](http://www.playframework.com/documentation/2.1.1/JavaRouting)
* XML support. Play Framework’s XML library is refreshingly simple to use compared to other Java XML libraries which tend to be very verbose. For example, you may be iterating through search results from the MarkLogic Java API and want to grab the text node value of one of the returned metadata elements: {% gist peterskim12/5710840 %} [http://www.playframework.com/documentation/2.1.1/JavaXmlRequests](http://www.playframework.com/documentation/2.1.1/JavaXmlRequests)
* JSON support [http://www.playframework.com/documentation/2.1.1/JavaJsonRequests](http://www.playframework.com/documentation/2.1.1/JavaJsonRequests)

I’m not going to go into the specifics of building a MarkLogic application using the Java API in this post but I may create some posts in the future if I come across anything that may be helpful. For now, I’d recommend getting started by reading Erik Hennum’s great article about how to use JAXB with the Java API:

[http://jaxenter.com/marklogic-pojos-44475.html](http://jaxenter.com/marklogic-pojos-44475.html)

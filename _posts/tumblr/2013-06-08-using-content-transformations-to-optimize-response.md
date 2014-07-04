---
layout: post
title: Using content transformations to optimize response sizes with the MarkLogic
  REST or Java API
date: '2013-06-08T14:55:00-04:00'
tags:
- MarkLogic
tumblr_url: http://peterskim.tumblr.com/post/52478875679/using-content-transformations-to-optimize-response
---
For application developers, sometimes the most important pieces of functionality aren’t the ones that the marketing folks like to publicize. The new REST API and Java API in MarkLogic 6 make writing data-driven applications in your favorite programming language a bit easier than it used to be with the old XCC API libraries. However, one important thing to take into account when developing applications with this model as opposed to writing applications in XQuery is that data transfer between the MarkLogic Server and your application server and how that contributes to query latency now needs to be considered.

Fortunately, the Java and REST APIs provide a mechanism called ‘content transformations’ to apply a transform written in XQuery or XSLT to various REST API service endpoints that accept a transform parameter. The two examples in the MarkLogic documentation involve a transformation you may want to apply when sending a document to MarkLogic. However, this functionality may be just as useful to apply a transform on requests to read documents from MarkLogic. In MarkLogic 7, you’ll be able to apply content transformations on the responses from the /v1/search endpoint, which will give you a lot more control over your search result responses and avoid additional queries you may have to make to get the data you need to render your page.

Usually, the ideal data model for a MarkLogic database is one that is denormalized in order to allow applications to take advantage of MarkLogic’s rich search functionality while avoiding expensive query-time joins. As a result, a “record” in MarkLogic may be significantly larger than the normalized records you typically see in relational databases. Data is denormalized for search but you typically don’t need to display the entire contents of a document in your application’s user interface. This is where a query-time content transformation comes in handy for sending over only the data that is required for your application.

You can find more information about content transformations using the REST API here:

[http://docs.marklogic.com/guide/rest-dev/transforms#chapter](http://docs.marklogic.com/guide/rest-dev/transforms#chapter)

or the Java API here:

[http://docs.marklogic.com/guide/java/transforms#chapter](http://docs.marklogic.com/guide/java/transforms#chapter)

After checking those out, if this is a feature you think you may use, you might want to take a look at the Roxy Deployer, which will help you manage and deploy these content transformations (along with many other aspects of your application such as database/index settings, REST API options and extensions, etc.):

[https://github.com/marklogic/roxy/wiki/Working-with-the-REST-API](https://github.com/marklogic/roxy/wiki/Working-with-the-REST-API)

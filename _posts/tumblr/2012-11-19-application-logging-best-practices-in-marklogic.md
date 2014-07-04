---
layout: post
title: Application logging best practices in MarkLogic
date: '2012-11-19T16:39:10-05:00'
tags: []
tumblr_url: http://peterskim.tumblr.com/post/36088278514/application-logging-best-practices-in-marklogic
---
One practice that most proper Java developers learn early on is to write log messages responsibly. I learned the hard way in a previous job. Error log messages were set up to be emailed out to the application support staff. I accidentally checked in a change that should have logged messages at debug level but were at error level. The support staff at the company were bombarded with thousands of email messages. Let’s just say those folks and our MS Exchange administrators were not happy.

Just from doing a simple Google search, I found these good tips on Java logging:

[http://javarevisited.blogspot.com/2011/05/top-10-tips-on-logging-in-java.html](http://javarevisited.blogspot.com/2011/05/top-10-tips-on-logging-in-java.html)

Good logging practices are no less important in a MarkLogic XQuery application. Proper debug logging is as straight-forward as this:

{% gist peterskim12/4114111 %}



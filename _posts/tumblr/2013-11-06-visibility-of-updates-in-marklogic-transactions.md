---
layout: post
title: Visibility of updates in MarkLogic transactions
date: '2013-11-06T15:34:00-05:00'
tags:
- MarkLogic
tumblr_url: http://peterskim.tumblr.com/post/66210178662/visibility-of-updates-in-marklogic-transactions
---
I’m a bit ashamed to admit that even after being a MarkLogic developer for a year and a half, I still make mistakes like this:

{% gist peterskim12/7343494 %}

This query will always return “NO” because any updates made in a transaction will not be visible until after the updating statement is completed.

For a query like the short example above, it will be executed as a single-statement transaction so the update (in this case, the result of the xdmp:document-insert() function) isn’t visible until the transaction is committed. In multi-statement transactions, the update will be visible in subsequent statements in the same transaction.

More information can be found in the MarkLogic documentation: http://docs.marklogic.com/guide/app-dev/transactions#id_85012

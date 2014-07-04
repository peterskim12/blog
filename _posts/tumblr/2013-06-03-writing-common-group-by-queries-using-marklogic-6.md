---
layout: post
title: Writing common GROUP BY queries using MarkLogic 6
date: '2013-06-03T11:29:00-04:00'
tags:
- MarkLogic
tumblr_url: http://peterskim.tumblr.com/post/52060621145/writing-common-group-by-queries-using-marklogic-6
---
MarkLogic 6 adds a new feature called Aggregate Functions. As described by the documentation:

>An aggregate function performs an operation over the values in one or more range indexes. For example, computing a sum or count over an element, attribute, or field range index. Aggregate functions are best used for analytics that produce a small number of results, such as computing a single numeric value across a set of range index values.

Aggregate functions use In-Database MapReduce, which greatly improves performance because:

* Analysis is parallelized across the hosts in a cluster, as well as across the database forests on each host.
* Analysis is performed close to the data.

This can be useful for your typical SQL GROUP BY queries where you aggregate a large number of records to compute some value.

Michael Blakeley has a good post about GROUP BY queries in XQuery here:

[http://blakeley.com/blogofile/archives/560/](http://blakeley.com/blogofile/archives/560/)

I use his example content to show how to write a query using the new aggregate functions. 

Here is some code to insert the sales records as one document per record:

{% gist peterskim12/5698807 %}

Since the aggregate functions rely on indexes, you’ll need to create element range indexes on the qty and product-name elements first. Then the query will look like this:

{% gist peterskim12/5698983 %}

And here are the results:

{% gist peterskim12/5698999 %}


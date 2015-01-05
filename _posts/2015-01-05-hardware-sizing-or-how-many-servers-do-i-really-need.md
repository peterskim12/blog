---
layout: post
title: Hardware sizing or "How many servers do I *really* need?"
tags: [elasticsearch]
---

# Sales engineering and hardware sizing #

One of my responsibilities as a presales engineer at Elasticsearch is to help prospective users of the ELK stack figure out how many and what kind of servers they'll need to buy to support their requirements. Production deployments of the ELK stack vary significantly. Some examples of use cases I've spoken to people about include:

* Collecting and analyzing Apache and Java app server logs that support a major big box retailer's e-commerce site.
* Security information and event management (SIEM) solution provided as a service by a major telecom/network company for its customers.
* Full-text search and faceted navigation for an apartment search website.
* Organization-wide desktop/laptop systems monitoring for a public school district.

You can run a legitimate Elasticsearch deployment with just 1 server or 200 servers. You may need the ability to ingest 1 million documents per second and/or support thousands of simultaneous search queries at sub-second latencies. Or your needs may be significantly more modest because you're just getting the website/mobile app for your startup off the ground.

So in response to the question, "How much hardware will I need to run Elasticsearch?", the answer is always, "It depends."

One element of this discussion involves figuring out the amount of disk required. For this blog post, I'll focus on the centralized logging use case. 

# Indexing logs, a million different ways

A typical log message can be anywhere between 200 bytes and 2000 bytes or more. This log message can contain various types of data: 

* numbers indicating response time or response size
* multi-word strings containing details of a Java exception message
* single-word strings that aren't really words but might be an identifier such as a computer's hostname
* something like an IP address that could potentially be used as a lookup key to identify geo-location using geoip

Even if the raw log message is 500 bytes, the amount of space occupied on disk (in its indexed form in Elasticsearch) may be smaller or larger depending on various factors. The best way to start making rough estimates on how much disk you'll need is to do some testing using representative data.

Apparently, there's word going around that the data volume in Elasticsearch experiences significant expansion during the indexing process. While this can be true due to Elasticsearch performing text analysis at index-time, it doesn't have to be true, depending on the types of queries you expect to run and how you configure your indexing accordingly. It's certainly not an "all or nothing" scenario -- you can configure certain text fields to be analyzed and others to not be analyzed, in addition to tune other parameters which can have a significant impact on disk utilization. A common question asked with regards to disk usage is whether Elasticsearch uses compression -- Elasticsearch does utilize compression but does so in a way that minimizes the impact on query latency.

## To analyze or to not_analyze

As mentioned above, the textual analysis performed at index time can have a significant impact on disk space. Text analysis is a key component of full text search because it pre-processes the text to optimize the search user experience at query time. 

Fields can be configured to be analyzed, not be analyzed, retain both analyzed and non_analyzed versions and also be analyzed in different ways. A great introduction to the analysis process in Elasticsearch can be found in [Elasticsearch: The Definitive Guide](http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/analysis-intro.html).

## Are you _all in?

The _all field is a field, which by default, contains values of all the fields of a document. This is extremely convenient when the user doesn't know the field(s) in which a value occurs so they can search for text without specifying a field to search against. However, there will be additional storage overhead if all of a document's fields are indexed as a part of the _all field in addition to being indexed in its own field. More information about the _all field can be found here: [http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping-all-field.html](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping-all-field.html)

## Doc values

One additional lever that can make a significant impact on disk usage is doc values. Doc values are a way to reduce heap memory usage. More details can be found here: [http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/doc-values.html](http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/doc-values.html).

## Replication

Elasticsearch is a distributed system and an assumption in distributed systems design is that hardware will fail. A well-designed distributed system must embrace this assumption and handle failures gracefully. One way in which Elasticsearch ensures resiliency is through the use of replication. Elasticsearch, by default, enables shard-level replication which provides 1 replica copy of each shard located on a different node. 

Obviously, if you have an additional copy of your data, this is going to double your storage footprint. Other centralized logging solutions do not enable replication by default, so when you're comparing an ELK-based solution to an alternative, you should consider whether replication is factored in.

## Tests

The test log file used for this test is a 67644119 byte log file. It contains Apache HTTP logs from a colleague's blog that look something like this:

~~~~
71.212.224.97 - - [28/May/2014:16:27:35 -0500] "GET /images/web/2009/banner.png 
HTTP/1.1" 200 52315 "http://www.semicomplete.com/projects/xdotool/" 
"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_2) AppleWebKit/537.36 
(KHTML, like Gecko) Chrome/32.0.1700.107 Safari/537.36"
~~~~

The testing process itself is straight-forward:

* Ingest the log file using Logstash with a simple config
* Optimize the index to 1 segment (for a consistently comparable size) by calling POST test_index/_optimize?max_num_segments=1
* Get the index size on disk by calling GET test_index/_stats
* Remove the index by calling DELETE test_index

Assumptions:

* There is no replication in this testing because it's done on a single node. If you are planning on enabling replication in your deployment (which I'd strongly recommend unless you really don't mind potentially losing data), you should increase your expected storage needs by your replication factor.
* The 'message' field generated by Logstash is removed. In case you aren't familiar with Logstash, it reads each line of input into a single 'message' field from which you ideally parse out all the valuable data elements. I removed the 'message' field because I don't see the value in keeping it and having it occupy valuable space if I am confident that I parsed out all the data I want from the log message. However, some folks may want to retain the log line in its original form, which would increase the storage footprint. 


Here is a summary of the test results:

<table border="1">
<tr><th>Test number</th><th>string fields</th><th>_all</th><th>doc_values</th><th>index size (in bytes)</th><th>Expansion ratio (index size / raw size)</th></tr>
<tr><td>1</td><td>analyzed and not_analyzed</td><td>enabled</td><td>enabled</td><td>94633818</td><td>1.399</td></tr>
<tr><td>2</td><td>analyzed and not_analyzed</td><td>disabled</td><td>enabled</td><td>75648416</td><td>1.118</td></tr>
<tr><td>3</td><td>not_analyzed</td><td>disabled</td><td>enabled</td><td>63079805</td><td>0.933</td></tr>
<tr><td>4</td><td>not_analyzed, except for 'agent' field which is indexed as analyzed</td><td>disabled</td><td>enabled</td><td>65010980</td><td>0.961</td></tr>
<tr><td>5</td><td>analyzed and not_analyzed</td><td>enabled</td><td>disabled</td><td>80608354</td><td>1.192</td></tr>
<tr><td>6</td><td>analyzed and not_analyzed</td><td>disabled</td><td>disabled</td><td>61680474</td><td>0.912</td></tr>
<tr><td>7</td><td>not_analyzed</td><td>disabled</td><td>disabled</td><td>48432487</td><td>0.716</td></tr>
<tr><td>8</td><td>not_analyzed, except for 'agent' field which is indexed as analyzed</td><td>disabled</td><td>disabled</td><td>51948672</td><td>0.768</td></tr>
</table>

Note: In the table above, when it says "analyzed and not_analyzed", this means mapping a single source field into multiple indexed fields that reflect different analysis -- one analyzed and the other not_analyzed. See more details regarding multi-fields here: [http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping-core-types.html#_multi_fields_3](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping-core-types.html#_multi_fields_3).


## Analysis of the results

As you can see from the table above, we see expansion/contraction ratios between 0.768 and 1.399 depending on how you configure the Elasticsearch mapping. For smaller deployments, this won't make a huge difference -- disk is relatively cheap and a 2x difference from the best case to worst case isn't a signficant variance. However, if you're planning for a larger deployment, it will certainly be worth having some intentionality in how you configure your mapping. 

For example, if you're expecting to ingest 5 TB of log data per day and store it for 30 days, you're looking at a difference between 115 and 210 TB in total storage needs when comparing the mappings with minimal vs maximum storage needs. Depending on other factors which will help define how much data you can host on each node while maintaining reasonable query performance, this could mean 25-50 extra nodes. And that's not even considering replication. 

While there are a number of dimensions in which you can make comparisons, I'll focus on a few. 

Disabling the _all field reduced the expansion factor from 1.399 to 1.118. This is a significant reduction in storage footprint which is an easy win if your users are familiar with the fields they want to search against. Even if you can't assume your users know what fields to search, you can customize your search application to take what the user perceives as an non-fielded search and construct a multi-field search query behind the scenes. 

Configuring the mapping to index most or all of the fields as "not_analyzed" reduced the expansion factor from 1.118 to 0.93 or 0.96. In the log analysis use case, realistically, many, if not, most of the fields don't represent data that makes sense to run textual analysis on. There are a lot of fields you'll certainly want to run aggregate analysis on (e.g. histograms, pie charts, heat maps, etc.) but these don't require text analysis.

Finally, the last area of focus is the impact of doc values. Looking at two mappings that are equivalent besides the doc values config, the difference in expansion factor is 1.399 and 1.192. Again, the types of queries you'll expect to run will drive whether you want to enable doc values or not. Heavy use of aggregations and sorting will probably benefit from using doc values.

# Conclusion

There are a lot of misconceptions out there about how much disk space an ELK-based solution requires but hopefully this blog post sheds some light on how the reality is that "it depends". 

You can find the files supporting this testing on Github here: [https://github.com/peterskim12/elk-index-size-tests](https://github.com/peterskim12/elk-index-size-tests).

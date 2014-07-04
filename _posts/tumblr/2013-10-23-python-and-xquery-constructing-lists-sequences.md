---
layout: post
title: 'Python and XQuery: constructing lists/sequences functionally'
date: '2013-10-23T21:40:43-04:00'
tags:
- python xquery
tumblr_url: http://peterskim.tumblr.com/post/64918108049/python-and-xquery-constructing-lists-sequences
---
When you learn XQuery, if you come from programming background in languages like Java or C#, it can be annoying finding out that you can’t do something simple like this:

{% gist peterskim12/7129907 %}

XQuery is a functional programming language, so variables are immutable. You can’t initialize an empty data structure then append to it. So the proper way to build this “array” is to include whatever logic you want to apply in the initial assignment. e.g.

{% gist peterskim12/7129935 %}

After a few months or years of primarily writing code in XQuery, when you go back to Java (or any other language), you will be surprised when you find yourself wanting to code using XQuery-ish syntax. Fortunately, some languages, like Python, support functional programming constructs. Python has this concept of list comprehension where you can construct lists in a similar fashion as XQuery:

{% gist peterskim12/7129950 %}


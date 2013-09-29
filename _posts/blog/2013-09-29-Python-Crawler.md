---
layout: post
title: 用python写爬虫
description: python, crawler
category: blog
---

##请求页面

	import urllib2
	response = urllib2.urlopen('http://www.baidu.com')
	print response.read()
	response.close()



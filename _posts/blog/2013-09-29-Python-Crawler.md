---
layout: post
title: 用python写爬虫
description: python, crawler
category: blog
---

##请求页面

	import urllib2
	response = urllib2.urlopen('http://www.baidu.com')
	html = response.read()

使用`urllib2.urlopen()打开页面，返回`response对象，`response类似于文件，使用`read()读取返回的页面内容。要注意填写的url字符串要包含url的类型，如http，ftp等，如果只写www.baidu.com，会抛出错误unknown url type。



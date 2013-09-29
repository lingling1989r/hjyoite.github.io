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

使用`urllib2.urlopen()`打开页面，返回`response`对象，`response`类似于文件，使用`read()`读取返回的页面内容。要注意填写的url字符串要包含url的类型，如http，ftp等，如果只写www.baidu.com，会抛出错误unknown url type。

	req = urllib2.Request('http://www.baidu.com')
	response = urllib2.urlopen(req)

也可使用`urllib2.Request()`构造url请求，通过`urllib2.urlopen(req)`请求页面。

	headers = {
		'Host' : 'www.baidu.com'
		'User-Agent' : 'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:24.0) Gecko/20100101 Firefox/24.0'
		'Referer' : 'http://www.baidu.com'
	}
	req = urllib2.Request(url='http://tieba.baidu.com', headers=headers)

在构造`Request`请求时，可以添加headers来模仿Http头信息（headers是字典类型）。其中，User-Agent用于模仿浏览器放出Http请求；Referer表明链接是从哪里发起，如我在http://www.baidu.com这个页面点击贴吧的链接http://tieba.baidu.com，那么在发起的Http请求中，我们可以看到它的Referer就是http://www.baidu.com。曾在某篇文章中看过，某些网站是通过检查Referer这个属性来判断外链，从而拒绝外链的访问，因此通过修改Referer，我们可以伪装站内请求，爬取所需的信息。而我个人的经验，在爬取新浪微博的某些信息时，也遇过需要填写Referer才能获取正确的返回内容。
为了研究从哪些url链接可以请求到我们要爬取的内容，以及Http头的哪些信息是在发起有效请求时所必须的，我们常常要捕获浏览器与服务器进行交互时的Http请求。对此，我使用的是Firefox上的一个插件[Live Http Headers][]，它能截获和显示我们需要的所有Http请求信息。其中的replay功能，还能修改截获请求中的信息，并重新发送给服务器。

[Live Http Headers]: http://livehttpheaders.mozdev.org/
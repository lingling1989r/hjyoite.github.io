---
layout: post
title: 用Python写爬虫
description: Python, crawler
category: blog
---

##请求页面

	import urllib2
	response = urllib2.urlopen('http://www.baidu.com')
	html = response.read()

`urllib2.urlopen()`打开网页页面，返回`response`对象，`response`类似于文件对象，通过使用`read()`函数读取返回的页面内容。其中要注意的是，填写的url字符串要包含url的类型，如http，ftp等，如果只写www.baidu.com，会抛出错误unknown url type。

	req = urllib2.Request('http://www.baidu.com')
	response = urllib2.urlopen(req)

也可使用`urllib2.Request()`构造url请求，通过`urllib2.urlopen(req)`请求页面。

	headers = {
		'Host' : 'www.baidu.com'
		'User-Agent' : 'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:24.0) Gecko/20100101 Firefox/24.0'
		'Referer' : 'http://www.baidu.com'
	}
	req = urllib2.Request(url='http://tieba.baidu.com', headers=headers)

在构造`Request`请求时，可以添加headers来模仿Http头信息（headers是字典类型）。其中，User-Agent属性用于伪装浏览器发出Http请求；Referer属性表明链接是从哪里发起。如我在http://www.baidu.com这个页面点击贴吧的链接http://tieba.baidu.com，那么在发起的Http请求中，我们可以看到它的Referer就是http://www.baidu.com。曾在某篇文章中看过，某些网站是通过检查Referer这个属性来判断外链，从而拒绝外链的访问，因此通过修改Referer，我们可以伪装成站内请求，爬取所需的信息。而我个人的经验，在爬取新浪微博的某些信息时，也遇过需要填写Referer才能获取正确的返回内容。
	
	import urllib, urllib2
	formdata = {
				"entry" : 'weibo',
				"service" : 'miniblog',
				"pwencode" : 'rsa2',
				"encoding" : 'UTF-8',
				"url" : 'http://weibo.com/ajaxlogin.php?framelogin=1&callback=parent.sinaSSOController.feedBackUrlCallBack',
				"returntype" : 'META'
				}
	req = urllib2.Request(url="http://login.sina.com.cn/sso/login.php?client=ssologin.js(v1.4.2)", data=urllib.urlencode(formdata))
	urllib2.urlopen(req)
Http请求分为了Get和Post两种类型，Get只向服务器请求数据，而Post则还向服务器发送数据，如在登录网站时，发送表单中的用户名和密码。Python中，我们首先使用字典类型存储要发送的数据，然后通过`urllib.urlencode)()`对其进行编码（注意是`urllib`，不是`urllib2`），最后作为参数data传入`urllib2.Request()`，构造Http的Post请求。


为了研究从哪些url链接可以请求到我们要爬取的内容，以及Http头的哪些信息是在发起有效请求时所必须的，我们常常要捕获浏览器与服务器进行交互时的Http请求。对此，我使用的是Firefox上的一个插件[Live Http Headers][]，它不但能截获和显示所有的Http请求。其中的replay功能，还能让我们修改截下来的请求中的信息，并重新将它们发送给服务器。

##分析页面
<ul>
	<li>`正则表达式`：适合提取具有特定模式的数据，但要写正则表达式，有时候比较麻烦。</li>
	<li>`HTMLParser`：Python自带的分析HTML的库，用起来不够简便。而且兼容性不够好，经常会抛出malformed tag之类的错误，特别是遇到JavaScript。</li>
	<li>`[BeautifulSoup][]`：用于分析HTML类文件的第三方库，简单易用，非常强大，而且兼容性也很好，</li>
</ul>

[Live Http Headers]: http://livehttpheaders.mozdev.org/
[BeautifulSoup]: http://www.crummy.com/software/BeautifulSoup/
---
title: 《Node与Express开发》读书笔记2
date: 2016-01-31 15:59:55
tags:
---

### 第6章

*这里最好结合《图解HTTP》来看*

#### URL的组成

	以http://google.com/search?q=grunt&first=9和http://localhost:3000/about?test=1#history为例

* 协议

常见的有`http`/`https`/`ftp`/`file`

* 主机名
`localhost`/IP地址/域名

* 端口
默认**80端口**负责HTTP传输，**443宽口**负责HTTPS传输，如果更改则需要一个大于1023的端口号，0~1023端口为“知名端口”

* 路径
路径是应用中的页面或其他资源的唯一标识

* 查询字符串 `/search` `/about`

	* 查询字符串是一种键值对集合，是可选的。
	
	* 以`？`开头，键值对则以`&`（与号）分隔开。

	* 所有的名称和值必须是URL编码的，js还提供了一个嵌入式的函数`encodeURlComponent`来处理。

	* jsz中有三个可以对字符串编码的函数：
		* escape()
		* encodeURI()
		* encodeURIComponent()
		*  `escape()`除了 ASCII 字母、数字和特定的符号外，对传进来的字符串全部进行转义编码，因此如果想对URL编码，最好不要使用此方法。而`encodeURI()` 用于编码整个URI,因为URI中的合法字符都不会被编码转换。`encodeURIComponent`方法在编码单个URIComponent（指请求参 数）应当是最常用的，它可以讲参数中的中文、特殊字符进行转义，而不会影响整个URL。
	
	* URL和URI的区别：
		* URI：Uniform Resource Identifier，统一资源标识符
		* URL：Uniform Resource Locator，统一资源定位符
		* URN：Uniform Resource Name，统一资源名称

* 信息片段 `#history`
信息片段（或称散列）被严格限制在浏览器中使用，不会上传到服务器，常用于控制单页应用或AJAX富应用

#### HTTP请求方法

1.在浏览器中键入一个地址或点击一个链接，服务器会受到一个HTTP`GET`请求，包括**URL路径**和**查询字符串**

2.`POST`请求常用来提交信息到服务器

3.还有其他HTTP方法，在Express总通常要针对特殊方法编写处理程序

#### 请求报头



#### 响应报头

#### 报头的组成

#### 媒体类型

#### 请求体

#### 参数

#### 请求对象

#### 响应对象

#### Express源码路径说明

#### Express中常用的的功能说明
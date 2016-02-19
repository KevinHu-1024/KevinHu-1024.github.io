---
title: 《Node与Express开发》读书笔记
date: 2016-01-31 10:05:25
tags:
---

### 第1~5章

#### 使用Node实现的简单的Web服务器

1.(P13)尽量避免在js中写HTML *->第七章*

2.(P15)响应吗 *->第六章*

#### 省时省力的Express-脚手架

3.(P18)Boilerplate脚手架（生成空白的H5网站）和Bootstrap *->第七章*

#### 省时省力的Express-初始步骤

4.(P20)Express文档中的`app.VERB`指的是HTTP动词（常见`get`和`post`），这个方法一共有两个参数——一个路径、一个参数

5.(P20)`app.VERB`默认忽略了**大小写**和**反斜杠**，并且在匹配时**不考虑查询字符串** ，相当于简化了原来自己创建node路由器时用来处理`path`变量的正则的步骤

6.(P20)**请求**和**响应对象** *->第六章*

7.原来的node服务器：
```javascript
var http = require('http');

http.creatSever(function(req, res){
	var path = req.url.replace(/\/?(?:\?.*)?$/, '').toLowerCase();
	switch(path){
		case '':
			res.writeHead(200, {'Content-Type': 'text/plain'});
			res.end('Homepage');
			break;
		case '/about':
			res.writeHead(200, {'Content-Type':'text/plain'});
			res.end('About');
			break;
		default:
			res.writeHead(404, {'Content-Type':'text/plain'});
			res.end('Not Found');
			break;
	}
}).listen(3000);
```

这是Express的服务器
```javascript
app.get('/', function(req, res) {
	res.type('text/plain');
	res.send('Homepage');
});
app.get('/about', function(req, res) {
	res.type('text/plain');
	res.send('About');
});
app.use(function(req, res, next)) {
	res.type('text/type');
	res.status(404);
	res.send('404-Not Found');
};
```
(P20)在Express中，`res.end`被替换成了Express的扩展`res.send`，还用`res.set`和`res.status`替换了Node中的`res.writeHead`。Express还提供了一个`res.type`方法，可以用来方便的设置响应头`Content-Type`。当然原来的node中的方法仍然可用，只是不推荐。

8.(P21)在Express中，处理**404**和**505**页面的处理与其他页面有所区别，这点与原生node服务器中的处理方式并不一样，它使用了`app.use`。`app.use`是Express中添加中间件的一种方法 *->第十章*

9.(P21)Express中，**路由和中间件的添加顺序**至关重要

10.路由器路径还支持通配符（如`/about/*`），注意这可能会导致顺序上的问题，可能会导致通配符所代表的其他路径永远无法匹配到如（`/about/a`、`/about/b`页面就被通配符替换掉了）

11.(P21)Express能根据回调函数中参数的个数来区分404和500处理器 *->第十章&第十二章*

#### 视图和布局

12.(p24)在使用视图引擎的时候，通常情况下，我们在路由器中不必指定`res.type`和`res.status`，视图引擎会默认返回`text/html`的内容类型和`200`的状态码。而在catch-all处理器（**404**、**500**）中，我们必须手工明确指定状态码

#### 视图和静态文件
13.(P24)Express靠中间件处理静态文件和视图。 *->第十章*

14.(P24)`static`中间件可以将**一个**或**多个**目录指派为包含静态资源的目录，其中的资源**不经过任何特殊处理**直接发送到客户端

15.(P24)应该把`static`中间件加在所有路由器之前（印证了之前9所说，中间件和路由器的顺序至关重要），使用`app.use(express.static(__dirname + '/public'));`但在自己的N-Blog项目中有所不同：
```javascript
// view engine setup
/*这里设置views文件夹为存放视图文件的目录，即存放模板文件的地方，_dirname为全局变量，存储当前正在执行的脚本所在的目录*/
app.set('views', path.join(__dirname, 'views'));

/*这里设置public文件夹为存放静态文件的目录*/
app.use(express.static(path.join(__dirname, 'public')));
```
需要研究一下`__dirname`、`app.set`、`app.use`、`path.join`的用法，还有中间件与模块调用语法的区别

16.`static`相当于给你想要的静态文件创建了一个路由，从而统一了静态文件的管理，`static`中间件会返回这个文件，并正确的设定为内容类型

#### 测试技术
17.测试的类型：

* 单元测试
单元测试的粒度非常细，是对单个组件进行测试以确保其功能正确，在测试逻辑时更实用

* 集成测试
集成测试是对多个组件甚至整个系统之间的交互进行测试

18.测试技术概览

* 页面测试
测试页面的表现及前端功能，同时涉及**单元测试**和**集成测试**，使用`Mocha`来测试

* 跨页测试
对从一个页面转到另一个页面功能的测试（跨页过程中可能携带了参数），设计**集成测试**，使用`Zombie.js`来测试

* 逻辑测试
逻辑测试会对逻辑域进行**单元测试**和**集成测试**，它只会测试js，跟所有表示功能**分开**

* 去毛
去毛是寻找**潜在**的错误，使用JSHint来测试

* 链接检查
发现破损链接，属于**单元测试**，使用`LinkChecker`来测试

19.页面测试
> 待补充，测试技术非常重要

20.跨页测试
> 待补充，测试技术非常重要

21.逻辑测试
> 待补充，测试技术非常重要

22.去毛
> 待补充，测试技术非常重要

23.链接检查
> 待补充，测试技术非常重要

24.使用Grunt实现自动化
> 待补充，测试技术非常重要

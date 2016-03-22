---
title: 探索Express中的render
date: 2016-02-27 18:32:09
tags: [javascript, express, http]
---

2016.02.27日志

* 看express源码（用到哪里看到哪里）

按照上面的想法，想要实现需要渲染的那部分由服务器渲染并返回。需要确定`res.render()`是如何运行、参量如何处理、以及如何返回客户端的。所以去查看了`express/lib/response.js`82行和777行对`res.render()`和它用到的`res.send()`的处理。得知，`res.render()`接收三个参数，分别是模板（视图）-选项（要传给模板引擎的）-回调函数，render渲染完模板之后如果没有设置回调函数，就会将渲染后的结果他们交给send方法进行发送，send方法检测到他们是string类型之后，会将他们以html的格式向客户端发送。这样我们在客户端接收到的实际上就是一个html格式的文档，符合我们最开始定下的预期。

<!-- more -->

另外一个需要确定的是从哪里去调用`res.render`，我们采用了从路由器调用/main路径的方式做的。应该还有其他请求的方式，比如说发送一段脚本让服务器执行后直接返回html。以后在做探索。

还有`res.render()`的第二个参数的类型，是一个对象包括很多键值对，还是一个键值对，值里是一个json？看到res.send方法中包含了对jsonp的支持代码，这个需要探索一下，这个第二参数怎么传，经过测试，第二参数随便怎么传都可以，但要保证第二个参数只是一个对象，`res.render`中还对第二参数进行了self.local处理？这是什么待探索。对这个文件修改完了之后要对服务器进行重启，否则会找不到数据，对视图的改动可以不用重启

另在app.js中对路由器的模块化导出，怎么做，应该把路由器放到一个单独的文件中进行维护，而不是和主程序混杂在一起。还有路由相关的几个变量注意一下，刚才在哪里坑了好久

`res.send`方法中，如果传入的数据是数字->状态码；字符串->html；布朗值->不处理；对象->空（去掉null，返回空字符串）/缓存（不知道怎么处理）/这几种都不是（json）

还是去看api吧[API](http://expressjs.com/zh-cn/4x/api.html#res.render)

准备向ES6/typescript进行切换

函数式编程->coffeescript

面向对象编程->typescript

[todo]关于exports和modules等用法，需要去参考CMD/AMD/commonJS的知识


```javascript
/**
 * Render `view` with the given `options` and optional callback `fn`.
 * When a callback function is given a response will _not_ be made
 * automatically, otherwise a response of _200_ and _text/html_ is given.
 *
 * Options:
 *
 *  - `cache`     boolean hinting to the engine it should cache
 *  - `filename`  filename of the view being rendered
 *
 * @public
 */

res.render = function render(view, options, callback) {
  var app = this.req.app;
  var done = callback;
  var opts = options || {};
  var req = this.req;
  var self = this;

  // support callback function as second arg
  if (typeof options === 'function') {
    done = options;
    opts = {};
  }

  // merge res.locals
  opts._locals = self.locals;

  // default callback to respond
  done = done || function (err, str) {
    if (err) return req.next(err);
    self.send(str);
  };

  // render
  app.render(view, opts, done);
};
```
```javascript
/**
 * Send a response.
 *
 * Examples:
 *
 *     res.send(new Buffer('wahoo'));
 *     res.send({ some: 'json' });
 *     res.send('<p>some html</p>');
 *
 * @param {string|number|boolean|object|Buffer} body
 * @public
 */

res.send = function send(body) {
  var chunk = body;
  var encoding;
  var len;
  var req = this.req;
  var type;

  // settings
  var app = this.app;

  // allow status / body
  if (arguments.length === 2) {
    // res.send(body, status) backwards compat
    if (typeof arguments[0] !== 'number' && typeof arguments[1] === 'number') {
      deprecate('res.send(body, status): Use res.status(status).send(body) instead');
      this.statusCode = arguments[1];
    } else {
      deprecate('res.send(status, body): Use res.status(status).send(body) instead');
      this.statusCode = arguments[0];
      chunk = arguments[1];
    }
  }

  // disambiguate res.send(status) and res.send(status, num)
  if (typeof chunk === 'number' && arguments.length === 1) {
    // res.send(status) will set status message as text string
    if (!this.get('Content-Type')) {
      this.type('txt');
    }

    deprecate('res.send(status): Use res.sendStatus(status) instead');
    this.statusCode = chunk;
    chunk = statusCodes[chunk];
  }

  switch (typeof chunk) {
    // string defaulting to html
    case 'string':
      if (!this.get('Content-Type')) {
        this.type('html');
      }
      break;
    case 'boolean':
    case 'number':
    case 'object':
      if (chunk === null) {
        chunk = '';
      } else if (Buffer.isBuffer(chunk)) {
        if (!this.get('Content-Type')) {
          this.type('bin');
        }
      } else {
        return this.json(chunk);
      }
      break;
  }

  // write strings in utf-8
  if (typeof chunk === 'string') {
    encoding = 'utf8';
    type = this.get('Content-Type');

    // reflect this in content-type
    if (typeof type === 'string') {
      this.set('Content-Type', setCharset(type, 'utf-8'));
    }
  }

  // populate Content-Length
  if (chunk !== undefined) {
    if (!Buffer.isBuffer(chunk)) {
      // convert chunk to Buffer; saves later double conversions
      chunk = new Buffer(chunk, encoding);
      encoding = undefined;
    }

    len = chunk.length;
    this.set('Content-Length', len);
  }

  // populate ETag
  var etag;
  var generateETag = len !== undefined && app.get('etag fn');
  if (typeof generateETag === 'function' && !this.get('ETag')) {
    if ((etag = generateETag(chunk, encoding))) {
      this.set('ETag', etag);
    }
  }

  // freshness
  if (req.fresh) this.statusCode = 304;

  // strip irrelevant headers
  if (204 == this.statusCode || 304 == this.statusCode) {
    this.removeHeader('Content-Type');
    this.removeHeader('Content-Length');
    this.removeHeader('Transfer-Encoding');
    chunk = '';
  }

  if (req.method === 'HEAD') {
    // skip body for HEAD
    this.end();
  } else {
    // respond
    this.end(chunk, encoding);
  }

  return this;
};
```
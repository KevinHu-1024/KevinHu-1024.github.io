---
title: Jade在前端渲染中的使用
date: 2016-02-22 18:19:21
tags: [javascript, jade, 前端渲染, 全栈]
---

做agui.com项目时遇到了前端渲染的问题，想用jade做前端渲染模板。

jade本为后端设计，但是它简洁的语法和强大的功能深深地吸引了我，遂[谷歌](http://stackoverflow.com/questions/10188618/client-side-server-side-templating-feels-wrong-to-me-how-to-optimize/13947257'%20target='_blank'%3EClient%20side%20+%20Server%20side%20templating,%20feels%20wrong%20to%20me,%20how%20to%20optimize?)之:

<!-- more -->

That's very easy to use Client side + Server side templating.When we are building some web apps,we should use ajax to get some data and use the callback function to deal with it.So we should render these data on the client side.

The question is how to render them on client side?

Now We just need a client side jade.js.

Follow this document : https://github.com/visionmedia/jade#readme

### First

```
git clone https://github.com/visionmedia/jade.git
```

### Second

```
$ make jade.js ( in fact the project has already compile the file for us )
```

so we just need to copy this file to the path that we use.


### Third

read my demo below :

```
<script type='text/javascript' language='javascript' src="lib/jquery-1.8.2.min.js"></script>
<script type='text/javascript' language='javascript' src="lib/jade/jade.js"></script>
<script type='template' id='test'>
ul
  li hello world 
  li #{item}
  li #{item}
  li #{item}
</script>
<script>
  var compileText = $("#test").text();
  console.log( typeof( compileText ) );
  var fn = jade.compile( compileText , { layout : false } );
  var out = fn( {item : "this is item " } );
  console.log( out );
  $("body").append( out );
</script>
```

Now you can see the output result in the body

```
hello world
this is item
this is item
this is item
```

After reading this demo I think that you would know how to seperate jade server side and client side.If you can understand which one compile the jade template,then all the questions are easy.

Maybe you would have another question now.How to write some jade template codes in *.jade?The document also provide us a way to do it.This Tutorial may help you.

index.jade

```
!!!5
html
  head
   title hello world
  body
    ul#list

    script#list-template(type='template')
      |- for( var i in data )
      |    li(class='list') \#{ data[i].name }
      |- }
```

index.js

```
/* you javascript code */
var compileText = $('#list-template').text();
var compile = jade.compile( compileText , { layout : false } );

var data = [{ "name" : "Ben" } , {"name" : "Jack" } , {"name" : "Rose" }];
var outputText = compile( data );

$("#list").append( outputText );
```


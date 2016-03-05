---
title: AJAX初探
date: 2016-02-19 16:50:15
tags: [学习, agui.com]
---
<!-- toc -->

在制作agui.com网站原型时，需要用到单页应用，自然涉及到了无刷新更新网页的技术。根据自己以前知道的知识，有两种方法来解决：原生AJAX和AngularJS/jQ等封装AJAX。

为了了解AJAX技术的实现原理及练习原生js，从原生AJAX开始学习：

### 1.XMLHttpReques/ActiveXObjectt对象是AJAX技术的基础

* 创建一个实例： `var anAjax = new XMLHttpRequest();`/`var anAjax = new ActiveXObject("Microsoft.XMLHTTP");`
* 兼容性问题：IE 5/6 使用 `ActiveXObject`对象来实现AJAX

	```javascript
	var anAjax;
	if (window.XMLHttpRequest)
	  {// code for IE7+, Firefox, Chrome, Opera, Safari
		  xmlhttp=new XMLHttpRequest();
	  } else {// code for IE6, IE5
		  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
	  }
	```

### 2.AJAX的实例方法`open(method,url,async)`

* open()方法规定了请求的类型/URL/是否异步

### 3.AJAX的实例方法`send(string)`

* send()方法将请求发送到服务器，*string*仅用于POST请求

### 4.请求类型GET/POST的选择

GET比较简单和快速，大部分情况下能用。

在下列情况下则使用POST请求：

* 无法使用缓存文件（更新服务器上的文件或数据库）
* 向服务器发送大量数据（POST没有数量限制）
* 发送包含未知字符的用户输入时，POST比GET更稳定可靠
* 扩展：参见文章（get和POST选择）

### 5.GET请求

* 基本

	```javascript
	xmlhttp.open("GET","demo_get.asp",true);
	xmlhttp.send();
	```

* 避免上面的代码获得缓存的结果

	```javascript
	xmlhttp.open("GET","demo_get.asp?t=" + Math.random(),true);
	xmlhttp.send();
	```

* 需要通过GET方法来发送信息

	```javascript
	xmlhttp.open("GET","demo_get2.asp?fname=Bill&lname=Gates",true);
	xmlhttp.send();
	```

### 6.POST请求

* 基本

	```javascript
	xmlhttp.open("POST","demo_post.asp",true);
	xmlhttp.send();
	```

* 需要像HTML表单一样POST数据，需要添加HTTP头信息，使用`setRequestHeader()`方法来添加头部，再将`send()`方法中的`string`形参设置为你的数据

	```javascript
	xmlhttp.open("POST","ajax_test.asp",true);
	xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
	xmlhttp.send("fname=Bill&lname=Gates");
	```

* `setRequestHeader(header,value)`方法：规定了头名称和头值

### 7.形参url-服务器上的文件地址

可以是任何类型的文件或服务器脚本，服务器脚本可以在传回响应之前，在服务器上执行任务

### 8.形参async-是否异步

* 异步时，浏览器发送请求之后不等待服务器响应，继续执行后面的工作

* 同步时，浏览器会等待服务器响应结果，如果事件较长，就会引起挂起或停止，所以我们一般选择异步发送，这样才是AJAX

* 异步时，我们需要给`onreadystatechange`绑定一个回调函数，用来执行，当服务器返回结果时，浏览器需要执行的程序，比如更新DOM元素

	```javascript
	xmlhttp.onreadystatechange=function() {
	  if (xmlhttp.readyState==4 && xmlhttp.status==200)
	    {
	    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
	    }
	  }
	xmlhttp.open("GET","test1.txt",true);
	xmlhttp.send();
	```

* 同步/异步可选，似乎说明了`XMLHttpRequest`对象还有除了AJAX之外的其他作用，文章结尾我将扩展一下`XMLHttpRequest`对象，来看一下它是不是还有其他作用

### 9.如何处理服务器响应

* `XMLHttpRequest`对象中有两个属性`responseText`和`responseXML`用来存储服务器返回的数据
* 如果服务器返回的数据并不是XML格式，则使用`responseText`属性来得到相应数据
* 如果服务器返回的数据时XML格式，则使用`responseXML`属性来获得相应数据，**同时**还要对XML对象进行解析：

	```javascript
	var xmlDoc=xmlhttp.responseXML;
	var str="";
	var dataList=xmlDoc.getElementsByTagName("ARTIST");
	for (i=0;i<dataList.length;i++) {
	  str=str + dataList[i].childNodes[0].nodeValue + "<br />";
	  }
	document.getElementById("myDiv").innerHTML=str;
	```
* 如果返回的是json呢？使用`responseText`获取来获取。然后4将其转为json（如`window.JSON.parse(str);`）*可行*

### 10.状态

* `onreadystatechange`事件属性

每次当`readystate`属性发生改变时，就会调用该函数

* `readyState`属性

0-请求为初始化

1-服务器连接已建立

2-请求已接收

3-请求处理中

4-请求已完成，且响应已就绪

* `status`属性

200-“OK”

404-未找到页面

* 当readyState=4且状态为200时，代表响应已就绪

	```javascript
	xmlhttp.onreadystatechange=function() {
	  if (xmlhttp.readyState==4 && xmlhttp.status==200) {
	    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
	    }
	  }
	```
### 11.使用AJAX的正确姿势——面向对象

```javascript
//类的构建定义，主要职责就是新建XMLHttpRequest对象
var MyXMLHttpRequest=function(){
    var xmlhttprequest;
    if(window.XMLHttpRequest){
        xmlhttprequest=new XMLHttpRequest();
        if(xmlhttprequest.overrideMimeType){
            xmlhttprequest.overrideMimeType("text/xml");
        }
    }else if(window.ActiveXObject){
        var activeName=["MSXML2.XMLHTTP","Microsoft.XMLHTTP"];
        for(var i=0;i<activeName.length;i++){
            try{
                xmlhttprequest=new ActiveXObject(activeName[i]);
                break;
            }catch(e){
                       
            }
        }
    }
    
    if(xmlhttprequest == undefined || xmlhttprequest == null){
        alert("XMLHttpRequest对象创建失败！！");
    }else{
        this.xmlhttp=xmlhttprequest;
    }
    
    //用户发送请求的方法
    MyXMLHttpRequest.prototype.send=function(method,url,data,callback,failback){
        if(this.xmlhttp!=undefined && this.xmlhttp!=null){
            method=method.toUpperCase();
            if(method!="GET" && method!="POST"){
                alert("HTTP的请求方法必须为GET或POST!!!");
                return;
            }
            if(url==null || url==undefined){
                alert("HTTP的请求地址必须设置！");
                return ;
            }
            var tempxmlhttp=this.xmlhttp;
            this.xmlhttp.onreadystatechange=function(){
                if(tempxmlhttp.readyState==4){
                    if(temxmlhttp.status==200){
                        var responseText=temxmlhttp.responseText;
                        var responseXML=temxmlhttp.reponseXML;
                        if(callback==undefined || callback==null){
                            alert("没有设置处理数据正确返回的方法");
                            alert("返回的数据：" + responseText);
                        }else{
                            callback(responseText,responseXML);
                        }
                    }else{
                        if(failback==undefined ||failback==null){
                            alert("没有设置处理数据返回失败的处理方法！");
                            alert("HTTP的响应码：" + tempxmlhttp.status + ",响应码的文本信息：" + tempxmlhttp.statusText);
                        }else{
                            failback(tempxmlhttp.status,tempxmlhttp.statusText);
                        }
                    }
                }
            }
            
            //解决缓存的转换
            if(url.indexOf("?")>=0){
                url=url + "&t=" + (new Date()).valueOf();
            }else{
                url=url+"?+="+(new Date()).valueOf();
            }
            
            //解决跨域的问题
            if(url.indexOf("http://")>=0){
                url.replace("?","&");
                url="Proxy?url=" +url;
            }
            this.xmlhttp.open(method,url,true);
            
            //如果是POST方式，需要设置请求头
            if(method=="POST"){
                this.xmlhttp.setRequestHeader("Content-type","application/x-www-four-urlencoded");
            }
            this.xmlhttp.send(data);
    }else{
        alert("XMLHttpRequest对象创建失败，无法发送数据！");
    }
    MyXMLHttpRequest.prototype.abort=function(){
        this.xmlhttp.abort();
    }
  }
}

```

### 扩展：关于`XMLHttpRequest`对象

* `XMLHttpRequest`对象用于在后台与服务器交换数据

* 常见用途：
  * 在不重新加载页面的情况下更新网页
  * 在页面已加载后从服务器请求数据
  * 在页面已加载后从服务器接收数据
  * 在后台向服务器发送数据

由此看来AJAX只是异步使用`XMLHttpRequest`对象的一种方式。

### 实验：自己搭建node后台服务器，模拟使用AJAX的情况

实验中我们采用Express，它已经为我们构建好了一个基础的服务器，现在我们来修改一下：

1.在index.ejs文件中</body>前后增加下面的代码：

	```javascript
    	<input id="btn" type="submit" value="1"/>
  	</body>
	<script type="text/javascript" src="javascripts/test.js"></script>
	```

2.在public文件夹下增加一个名为html的文件（没有扩展名），内容如下：

	```javascript
	AJAX test pass!</br>
         ----Kevin's Coffee
	```

3.在public/javascripts文件夹下增加test.js文件，内容如下：

	```javascript
	var btn = document.getElementById("btn");
	var x = new XMLHttpRequest();
	x.onreadystatechange=function() {
	  if (x.readyState==4 && x.status==200)
	    {
	    document.body.innerHTML=x.responseText;
	    }
	  }
	btn.onclick = function () {
	alert("Ready?");
	alert("Go!");
	x.open("GET","html",true);
	x.send();
	}
	```

4.回到根目录，运行npm start，在网页中点击按钮，我们可以看到点击之后，整个body的内容在没有页面刷新的情况下，已经被替换成了我们的html文件中的那一堆字符串，效果如下：

![点击之前](/images/first-ajax-1.png)

![点击之后](/images/first-ajax-2.png)

![同时可以查看服务器的记录](/images/first-ajax-3.png)

### 总结

![总结](/images/first-ajax-4.png)



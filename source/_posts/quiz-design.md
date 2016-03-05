---
title: 问卷调查系统之——问卷类的设计
date: 2016-02-29 16:52:59
tags:
---

# 问卷调查系统之——问卷类的设计

<!-- toc -->

## 感谢

[参考项目1](http://blog.csdn.net/daowzq/article/details/34827955)

这个项目值得借鉴的是它的`SurveyRazor.js`问卷渲染引擎，里面似乎实现了包括问卷类在内的所有功能，引入网页中时只需要下面的代码：

```javascript
//需要事先引入数据文件
//<script src="JsonData.js" type="text/javascript"></script>
SurveyRazor.dataStore.load(data);
SurveyRazor.surveyRazor.options({
    description: desc, //描述
    haveBgImg: false,  //启用背景图片
    surveyTitle: "客戶滿意度調查表"
}).create().show();
```
可是作者并没有给出json源数据及任何参考文档，我们需要手工分析这个引擎的结构，及使用方法

**最后这个项目中参考了它对问卷分类及对问题中必要属性的设置**

[参考项目2](https://github.com/LiangCY/questionnaire)

这个项目值得借鉴的是它的后台系统设计，完成了问卷问题的增删改及数据统计工作。

缺点是它的问题类型只有有限的几种，没有前者那么丰富;作者采用了react和VUE来开发的，虽然也能大概看清业务逻辑，但时间成本略高

[参考项目3](http://www.cocoachina.com/cms/wap.php?action=article&id=13440)
[它的Github](https://github.com/Double-Lv/QuestionMaker)
[教程](http://www.cnblogs.com/lvdabao/p/mean-techstack-angular.html)

这个项目值得参考的是它的后台系统设计，完成了问卷问题的增删改及预览工作。

缺点是它和我们的业务并不完全一样，主要展示了angularJS的双向绑定和构建单页应用

而且没有后台统计和权限管理功能，全部由angularJS来处理，连jade模板都不用了，都是ng模板直接拼接

然而难能可贵的是作者的博文中有学习angularJS的[笔记](http://www.cnblogs.com/lvdabao/tag/AngularJs/)，可以共学习angularJS时参考

**最后主要借鉴了这个项目中对问题和问卷类行为的设计**

[参考项目4](http://www.cnblogs.com/lvdabao/p/mean-techstack-angular.html)
[更多](http://angularjs4u.com/demos/5-angularjs-quiz-demos/)

这个是国外小哥写的4个问卷项目，还没细看

[参考项目5-n-blog]()

主要借鉴了这个项目中使用mongodb原生驱动的操作，用户类行为设计，session/flash的操作

**最后主要借鉴了它对session/flash的操作**

[参考资料6](https://docs.mongodb.org/master/MongoDB-crud-guide-master.pdf)

MONGODB 增删改查官方手册

MONGOOSE [官方api手册](http://mongoosejs.com/docs/index.html)

实现id自增的官方[方法](https://docs.mongodb.org/manual/tutorial/create-an-auto-incrementing-field/)

**最后主要选择了mongoose来操作数据库，同时对项目三的代码进行了重构**

有些方法调用语法没变，方法内部没做什么特殊操作，mongoose也提供了原生方法，实在没有必要再封装一次。项目三的作者从java出身，封装的习惯确实蛮好，但这里我认为实在没有必要封装，藉由js模块已经可以处理的很好了，没有必要对行为类再次抽象

>Each document can be saved to the database by calling its save method

## 分析参考项目1


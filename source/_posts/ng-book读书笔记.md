---
title: ng-book读书笔记
tags:
  - angularJS
  - 学习
date: 2016-02-22 20:27:04
---

# Data Binding and Your First AngularJS Web Application

## AngularJS的脏检测机制（dirty checking）

<!-- more -->

1. 当AngularJS认为被监视的值可能要发生更改，即去调用`$digest()`检测值是否被修改，同时会检测潜在的被修改的值

2. “脏检测”机制在检测数据模型更改时时一种高效的方案

3. 这里涉及到了几个知识：`脏检测` `event loop/digest cycle` `$digest()函数`

### 什么是脏检测？

> It checks whether a value has changed that hasn’t yet been synchronized across the app.  

>*——《ng-book》P427*

![dirty check](/images/ng-book/ng-book1.jpeg)

脏检测就是检测新值与旧值是否相等，不相等为脏。

### 关于event loop/digest cycle

* set a wathcer and watching $scope
* value changed -> AngularJS kicks and iterating the watch list
* function will execute on the value and triggers the validations and formatters to run
* 这一次循环完成，AngularJS还要再从头执行遍历一次，直到没有任何“脏东西”为止，循环退出，更新DOM

***

*问题1：如何监测到这个循环执行了多少次？就是监测DOM更新的频率？特别是watching一个连续变化的值（比如拖动）时，能不能跟得上鼠标？性能如何？*

*问题2：DOM更新是重排还是重绘？性能如何？*

*问题3：如果碰到了`a=b；b=a;`这种值时会怎么样？*

***

* 如果是点击事件，`ng-click`实际是绑定了浏览器原生的`click`事件，`ng-click`调用`$scope.$apply()`，然后再进入`$digest`循环

***

*问题4：`$scope.$apply()`的作用？*

***

### `$scope.$apply()`的作用？

> The $apply() function executes an expression inside of the Angular context from outside of the
Angular framework.

>*——《ng-book》P433*

想让外部函数在AngularJS作用域中执行时，或者让一个字符串变成一个js语句在AngularJS作用域中起作用时，调用`$apply()`方法，在这里的作用就是，本来点击事件发生在AngularJS外部，但是我们需要让他在AngularJS作用域内部执行，以便使用我们的处理函数（外面是无法访问里面的，所以要在里面执行），所以才这样调用。

## 数据绑定的最佳习惯

数据绑定在对象的一个属性上，而不是对象本身上

***

# Modules

## **模块**是定义AngularJS应用的主要方式，一个应用可以有多个模块负责不同的功能

***

*问题：模块间如何通信？向`$rootScope`暴漏api?*

***

## 使用模块的好处：

* 不污染全局作用域
* 容易测试，代码简洁，分离功能（我最喜欢的设计方式）
* 不同应用之间代码容易复用
* 应用可以按不同顺序加载各部分代码，方便维持依赖关系

## 模块相关方法

* 使用 angular.module(*String:name*, *Array:listOfDependencies*)（typescript大法好，严格类型下看方法都方便多了）方法来声明模块，它包含两个参数，一是模块名称，二是依赖列表，也称依赖注入

* angular.module(*String:name*, *Array:listOfDependencies*) 是模块的`setter`方法，定义模块

* angular.module(*String:name*) 是模块的`getter`方法，调用最近引用的模块（意味着如果模块同名，定义会被最新的覆盖）

***

# Scopes

## 作用域是Angular应用的核心

1. 作用域和Model是关联的，同时也是表达式的作用域

2. `$scope`对象中我们用来定义业务逻辑、控制器的方法、视图中的属性

3. 作用域是连接视图和控制器的胶水，有利于实现`promise`

![gule between C&V](/images/ng-book/1.jpg)

***

*问题1：`promise`是啥？为什么有利于实现它？*

***

4. 因为有实时绑定的存在，故作用域能够最真实的反映程序的状态

5. `$scope`实现了类似于DOM的层级结构，子作用域可以调用父作用域中的属性。AngularJS实际上是为DOM元素创建了它的私有作用域

6. 作用域提供了监视`Model`改变的能力

7. 我们在作用于中定义及执行表达式，同时可以在控制器之间甚至应用程序内不同组件之间传递事件

8. 最好将程序逻辑放在控制器中，将所需数据放在scope中

## 从$scope的视角看整个程序

*关于*The $scope View of the World*这一节的题目，翻译的实在太水，题目本来的意思是“从$scope的视角看整个程序”，结果被翻译成了“视图和 $scope 的世界”，然后里面还并没有细讲视图的事情，实在是呵呵…*

1. 关于`$rootScope`：可以理解为angularJS中的“全局作用域”，里面可以包含很多子级作用域。与原生js同样的，最好不要污染这个全局作用域

![$rootScope](/images/ng-book/2.jpg)

2. `$scope`对象可以理解为一个简单的js对象，可以随意更改里面的属性。换句话说它作为纯粹的Model，并不具备处理和操作数据的能力，只是衔接了视图与控制器而已

3. **View 可以访问到 $scope 中的所有数据**

***

问题2：是每一个$scope吗？还是这个视图所在控制器的作用域及其父级作用域的$scope？是否遵循原生js的作用域链机制？

***

4. AngularJS渲染模板中可以使用的标记有：

* 指令
* 数据绑定
* 过滤器
* 表单控制

## 作用域的作用

![what scopes do](/images/ng-book/3.jpg)

1. 再次强调**作用域能够最真实的反映程序的状态**，可以理解为作用域是ViewModel，这就是MVVM的由来

2. 在DOM元素上使用`ng-controller`命令，可以向这个DOM元素添加一个控制器

![myApp](/images/ng-book/4.jpg)

## 关于`$scope`的生命周期

![scope lifecycle](/images/ng-book/5.jpg)

1. 如何触发回调？

当浏览器收到一个执行与AngularJS作用域内的函数回调时，`$scope`会意识到Model可能发生了变化。而触发回调指的是：如 `ng-model`监控的`<input>`或`ng-click`监控的`<button>`发生了状态或值的改变，就会触发对应绑定的控制器函数。

2. 再次提到使用`$apply()`可以强制`$scope`之外的表达式执行于`$scope`之内

3. 生命周期：**Creation** -> **Linking** -> **Updating** -> **Destruction**

![scope creation](/images/ng-book/6.jpg)
![scope linking](/images/ng-book/7.jpg)
![scope updating](/images/ng-book/8.jpg)

4. 在**Destruction**完成之后，`$destroy`事件将被触发出来

## 命令与作用域

一般情况下，命令不会创建他们自己的`$scope`，除了下面的情况：

`ng-controller`和`ng-repeat`会创建他们自己的子`$scope`，并把他们添加到DOM元素中

***

# Controllers

控制器为了增强视图的功能而存在，AngularJS中的控制器会向View的作用域内添加额外的功能

控制器的命名规范*[Name]*Controller

当控制器创建`$scope`时，AngularJS就会把它调用了一次

创建控制器时，不要污染全局作用域，应该把它放在一个模块中隔离开来

想在View中创建自定义动作，我们需要用在控制器的作用域内创建函数

想绑定按钮或链接，我们需要另一种方式：`ng-click`指令。`ng-click`指令把它的处理函数绑定给了浏览器原生的`mouseup`事件，这也是为什么我们在这里需要使用`$scope.$apply()`因为，如果使用这个，那么`ng-click`指令的处理函数就会执行在浏览器的全局作用域中而不是AngularJS的作用域中

`ng-click`调用处理函数时要带参量

再次提到父级`$scope`中的属性可以被其自己访问

## 写控制器的最佳习惯

保持控制器足够精巧

控制器可以在一个独立的容器中把封装与一个独立视图相关的业务逻辑，使用依赖注入特性可以完成这个目的（比如声明模块是传入的依赖列表）

AngularJS与其他js框架的区别在于，控制器不适合做任何DOM操作、格式化、数据处理、以及其他超出**存储**Model数据的行为，他只是连接了View和Model

***

问题：

Controller不适合做DOM操作、格式化、数据处理，不建议添加业务逻辑。只能存储Model数据，只是连接了View和Model

Model不能处理和操作数据，只是衔接了视图与控制器而已

那么DOM操作被AngularJS中的`$digest()`接手了，数据处理和业务逻辑怎么办？

***

## 控制器嵌套层级（作用域套作用域）

ng应用的每个部分都有父作用域，无论它在哪个上下文中被渲染

当然也有例外，在指令内部创建的作用域成为`isolate（隔离）`作用域

AngularJS依靠DOM层级来区分父子作用域

再次强调要保持控制器的精巧，不要再其中进行DOM操作

# Expressions

表达式的几个重要点：

* 表达式执行于上下文中，能够访问到本地的作用域
* 当它遇到TypeError和ReferenceError时不会抛出异常
* 表达式中不允许任何控制流（比如if/else）
* 表达式能够访问过滤器或过滤器链

由于表达式运行于调用它的作用域中，它可以调用在其范围内那些绑定在作用域上的变量，也就是说我们可以循环变量、调用函数、或对变量进行数学计算

## 表达式的解析

表达式的解析分为自动和手动，一般情况下AngularJS会在`$diest`循环中自动完成解析。

但有些时候，我们需要手动解析表达式，这时就需要调用AngularJS中的内置服务`$parse`来帮我们完成解析，`$parse`能够访问到当前作用域下原始的js数据和函数

在手动解析表达式时，我们需要将`$parse`服务注入到控制器中，并且调用这个服务来解析表达式。

如：

```javascript
var aaa = function($scope, $parse) { // 注入服务
    ...
    var parseFun = $parse(newVal); // 调用服务
    ...
}
```

## 插入字符串


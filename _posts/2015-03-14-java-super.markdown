---
layout: post
title:  "Java中super的用法"
date:   2015-03-14
categories: java
author: 张翔
location: NanChang, China
cover: ""
description: Java中super的用法
---
---

super出现在继承了父类的子类中。有三种存在方式：
1. 第一种super.xxx;(xxx为变量名或对象名)这种方法意义为，获取父类中的名字为xxx的变量或方法引用。使用这种方法可以直接访问父类中的变量或对象，进行修改赋值等操作
2. 第二种super.xxx();(xxx为方法名)这种方法意义为，直接访问并调用父类中的方法。
3. 第三种super();这种方法意义为，调用父类的初始化方法，其实就是调用父类中的public xxx()方法

追问: 第三种在写代码的时候可以省略吗.?

回答:

看情况，比如说，你自己从新封装了jdk里面的HashMap类，public class MyMap extends HashMap

这时候，如果你想在实际应用中使用自己的类代替系统中原有的HashMap，就需要写如下代码

```java
public MyMap(){
super();
}
```

这样，你自己写的类`MyMap map = new MyMap();`才能使用这样做的好处是，你可以不知道原来父类的初始化构造函数是怎么写的。但是却可以轻易封装它。
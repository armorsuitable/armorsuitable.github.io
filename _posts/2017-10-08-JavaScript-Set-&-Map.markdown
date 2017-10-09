---
layout: post
title:  "JavaScript Map & Set"
date:   2017-08-02 19:13:05 +0800
categories: jekyll update
---

#### JavaScript的默认对象表示方式`{}`可以视为其他语言中的`Map`或`Dictionary`的数据结构，即一组键值对。


但是JavaScript的对象有个小问题，就是键必须是字符串。但实际上Number或者其他数据类型作为键也是非常合理的。
为了解决这个问题，最新的`ES6`规范引入了新的数据类型`Map`。要测试你的浏览器是否支持ES6规范，请执行以下代码，如果浏览器报ReferenceError错误，那么你需要换一个支持ES6的浏览器：

{% highlight javascript %}

'use strict';

var m = new Map();
var s = new Set();
// assert 
alert('你的浏览器支持Map和Set！');

{% endhighlight %}


### Map

**Map是一组键值对的结构，具有极快的查找速度。**
举个例子，假设要根据同学的名字查找对应的成绩，如果用Array实现，需要两个Array：


{% highlight javascript %}

var names = ['Michael', 'Bob', 'Tracy'];
var scores = [95, 75, 85];

{% endhighlight %}

给定一个名字，要查找对应的成绩，**就先要在names中找到对应的位置，再从scores取出对应的成绩，Array越长，耗时越长。**

如果用Map实现，只需要一个“名字”-“成绩”的对照表，直接根据名字查找成绩，无论这个表有多大，查找速度都不会变慢。用JavaScript写一个Map如下：


{% highlight javascript %}

var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95

{% endhighlight %}


初始化Map需要一个二维数组，或者直接初始化一个空Map。Map具有以下方法：

{% highlight javascript %}

var m = new Map(); // 空Map
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined

{% endhighlight %}


由于一个key只能对应一个value，所以，多次对一个key放入value，后面的值会把前面的值冲掉：

```
var m = new Map();
m.set('Adam', 67);
m.set('Adam', 88);
m.get('Adam'); // 88
```


### Set
Set和Map类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在Set中，没有重复的key.

要创建一个Set，需要提供一个Array作为输入，或者直接创建一个空Set：

```
var s1 = new Set(); // 空Set
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
```

重复元素在Set中自动被过滤：

```
var s = new Set([1, 2, 3, 3, '3']);
s; // Set {1, 2, 3, "3"}
```

注意数字3和字符串'3'是不同的元素。

通过add(key)方法可以添加元素到Set中，可以重复添加，但不会有效果：

```
s.add(4)
s
{1, 2, 3, 4}
 s.add(4)
 s
{1, 2, 3, 4}
```

通过delete(key)方法可以删除元素：

```
var s = new Set([1, 2, 3]);
s; // Set {1, 2, 3}
s.delete(3);
s; // Set {1, 2}
```                            
                        
                        
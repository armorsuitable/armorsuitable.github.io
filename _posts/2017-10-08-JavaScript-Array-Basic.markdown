---
layout: post
title:  "JavaScript 数组和对象基本操作"
date:   2017-07-03 19:58:05 +0800
categories: jekyll update
---

#### 数组：
JavaScript的Array可以包含`任意数据类型`，并通过索引来访问每个元素, 要取得Array的长度，直接访问length属性：

{% highlight javascript %}

var arr = [1, 2, 3.14, 'Hello', null, true];
arr.length; // 6

{% endhighlight %}


**`请注意`**，直接给Array的**`length`**赋一个新的值会导致Array大小的变化：

{% highlight javascript %}

    var arr = [1, 2, 3];
    arr.length; // 3
    arr.length = 6;
    arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
    arr.length = 2;
    arr; // arr变为[1, 2]

{% endhighlight %}

**请注意，如果通过索引赋值时，索引超过了范围，同样会引起Array大小的变化：**


{% highlight javascript %}

    var arr = [1, 2, 3];
    arr[5] = 'x';
    arr; // arr变为[1, 2, 3, undefined, undefined, 'x']

{% endhighlight %}


>大多数其他编程语言不允许直接改变数组的大小，越界访问索引会报错。


#### 然而，JavaScript的Array却不会有任何错误。在编写代码时，不建议直接修改Array的大小，`访问索引时要确保索引不会越界`。

---  

### 对象

>JavaScript的对象是一种无序的集合数据类型，它由若干键值对组成。

JavaScript的对象用于描述现实世界中的某个对象。例如，为了描述“小明”这个淘气的小朋友，我们可以用若干键值对来描述他：

{% highlight javascript %}

 var xiaoming = {
        name: '小明',
        birth: 1990,
        school: 'No.1 Middle School',
        height: 1.70,
        weight: 65,
        score: null
    };

{% endhighlight %}

JavaScript用一个**`{...}`**表示一个对象，键值对以**`xxx: xxx`**形式申明，用,隔开。注意，最后一个键值对不需要在末尾加,，如果加了，有的浏览器（如低版本的IE）将报错。

**上述对象申明了一个name属性，值是'小明'，birth属性，值是1990，以及其他一些属性。最后，把这个对象赋值给变量`xiaoming`后，就可以通过变量`xiaoming`来获取小明的属性了：**

{% highlight javascript %}


xiaoming.name; // '小明'
xiaoming.birth; // 1990


 var xiaohong = {
    name: '小红',
    'middle-school': 'No.1 Middle School'
};

{% endhighlight %}

**xiaohong的属性名`middle-school`不是一个有效的变量，就需要用''括起来。访问这个属性也无法使用.操作符，必须用`['xxx']`来访问：**

                        
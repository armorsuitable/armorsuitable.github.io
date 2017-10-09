---
layout: post
title:  "PHP 高精度运算的简单实现"
date:   2017-05-26 21:08:35 +0800
categories: jekyll update
---

首先PHP 自身语言的特性是弱类型，语言的弱类型必将产生的类型转换的问题

>首先看一段代码:

{% highlight php %}

  $a = 0.1;
  $b = 0.7;
  var_dump(($a + $b) == 0.8);  //false

{% endhighlight %}

得到的结果是 **False !**

关于 PHP Float类型 的说明官方给出的是 ：

>浮点数的精度有限。尽管取决于系统，PHP 通常使用 IEEE 754 双精度格式，则由于取整而导致的最大相对误差为 1.11e-16。非基本数学运算可能会给出更大误差，并且要考虑到进行复合运算时的误差传递。

详情见[http://php.net/manual/zh/language.types.float.php](http://php.net/manual/zh/language.types.float.php)


**总结下来就是：** 
> 由于内部表达方式的原因，比较两个浮点数是否相等是有问题的。不过还是有迂回的方法来比较浮点数值的。

**所以就是说简单的十进制分数如同 0.1 或 0.7 不能在不丢失一点点精度的情况下转换为内部二进制的格式。这就会造成混乱的结果**

>例如，floor((0.1+0.7)*10) 通常会返回 7 而不是预期中的 8，因为该结果内部的表示其实是类似 7.9999999999999991118...。


所以永远不要相信浮点数结果精确到了最后一位，也永远不要比较两个浮点数是否相等。如果确实需要更高的精度，应该使用任意精度数学函数或者 gmp 函数。

#### 任意精度数学函数

  bcadd — 将两个高精度数字相加 
  bccomp — 比较两个高精度数字，返回-1, 0, 1 
  bcdiv — 将两个高精度数字相除 
  bcmod — 求高精度数字余数 
  bcmul — 将两个高精度数字相乘 
  bcpow — 求高精度数字乘方 
  bcpowmod — 求高精度数字乘方求模，数论里非常常用 
  bcscale — 配置默认小数点位数，相当于就是Linux bc中的”scale=” 
  bcsqrt — 求高精度数字平方根 
  bcsub — 将两个高精度数字相减


例如使用下面的 bcadd() 函数

{% highlight php %}

  $a = 0.1;
  $b = 0.7;
  var_dump(bcadd($a,$b,2) == 0.8);

{% endhighlight %}
  
可以得到正确的结果

---

- 如果希望了解有关何时和如何将字符串转换成浮点数的信息，请参阅“[字符串传换为数值](http://php.net/manual/zh/language.types.string.php#language.types.string.conversion "字符串传换为数值")”一节。
- 对于其它类型的值，其情况类似于先将值转换成整型，然后再转换成浮点。请参阅“[转换为整型](http://php.net/manual/zh/language.types.integer.php#language.types.integer.casting)”一节以获取更多信息。

- 自 PHP 5 起，如果试图将对象转换为浮点数，会发出一条 E_NOTICE 错误消息。







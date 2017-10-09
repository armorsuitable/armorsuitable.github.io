---
layout: post
title:  "JavaScript 常用算法函数实现（一）"
date:   2017-07-18 21:01:05 +0800
categories: jekyll update
---            

####  原型函数

JS 的数组中列举出了系列的原型函数，其中包括常见的排序函数，其实现为下面：

{% highlight javascript %}

var apps = [2,53,1,43,42, 45,443, 4];
apps.sort(function(a, b){return a-b;});
console.log(apps);

{% endhighlight %}

使用任意的函数参数排序，数组（对arguments 数组排序）：


{% highlight javascript %}

function mySort() {
    var tags = new Array();
    //使用数组作为参数存储容器
    for(var i = 0;i < arguments.length;i++) {
        tags.push(arguments[i]);
    }
    tags.sort(function(compare1,compare2) {
        return compare1- compare2;
    });
    //返回已经排序的数组
    return tags;
}

var result = mySort(50,11,16,32,24,99,57,100);
console.info(result);

{% endhighlight %}
	
	
####  在数组的原型上添加方法：



>Array本地对象增加一个原型方法，它用于删除数组条目中重复的条目

**``(可能有多个),  返回值是一个包含被删除的重复条目的新数组。``**

{% highlight javascript %}
	
Array.prototype.distinct = function() {
    var ret = [];
    for (var i = 0; i < this.length; i++){
        for (var j = i+1; j < this.length;) {   
            if (this[i] === this[j]) {
                ret.push(this.splice(j, 1)[0]);
            } else {
                j++;
            }
        }
    }
    return ret;
}
//for test
alert(['a','b','c','d','b','a','e'].distinct());

{% endhighlight %}


### Reduce  

>reduce(callback, initialValue)会传入两个变量。回调函数(callback)和初始值(initialValue)。

1. 假设函数它有个传入参数，prev和next,index和array。prev和next你是必须要了解的。

{% highlight javascript %}

var arr = ["apple","orange","apple","orange","pear","orange"];
 
function getWordCnt(){
	var obj = {};

	for(var i= 0, l = arr.length; i< l; i++){
		var item = arr[i];
		obj[item] = (obj[item] +1 ) || 1;
		//JSON对象  obj["apple"] = 0 ||
		// {
		// 	   apple: 1,
		// 	   orange: 1,
		// }
	}

	return obj;
}

console.log(getWordCnt());

{% endhighlight %}

2. 一般来讲prev是从数组中第一个元素开始的，next是第二个元素。但是当你传入初始值(initialValue)后，

3. 第一个prev将是initivalValue，next将是数组中的第一个元素。

{% highlight javascript %}

var arr = ["apple","orange","apple","orange","pear","orange"];

function getWordCnt(){
	return arr.reduce(function(prev,next){
		prev[next] = (prev[next] + 1) || 1;
		return prev;
	},{});
}

console.log(getWordCnt());

{% endhighlight %}

#### JS数组操作添加、合并、排序


{% highlight javascript %}

function add_data_to_array_last( data_arrays, data )
{
    //从数组尾部增加一个数据
    return data_arrays.push(data);
}

function add_data_to_array_first( data_arrays, data )
{
    //从数组开头增加一个数据
    return data_arrays.unshift(data);
}

// 合并两个数组并使之排序
function combine_array_with_sorted (array_a, insert_index ,array_b)
{
    var result = array_a.concat();
    for (var i = 0 ; i != array_b.length; i++)
    {
	    result.splice(insert_index + i, 0 , array_b[i]);
    }
    return result;
}

{% endhighlight %}
                        
(未完待续 (╯^╰))

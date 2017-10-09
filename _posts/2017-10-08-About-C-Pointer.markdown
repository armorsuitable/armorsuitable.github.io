---
layout: post
title:  "线性查找 C-泛型编程"
date:   2017-06-12 21:31:05 +0800
categories: jekyll update
---

# 线性查找 C-泛型编程

基于 C中的通用类型指针 `(void *)`  可以接收任意类型的指针，故基于此理论就形成了泛型数据的线性查找算法。

查找的实现原理代码如下

{% highlight c %}

    #include <stdio.h>
    #include <stdlib.h>
    
    // 这里的函数是基于测试声明的，无任何实际项目的意义
    int intCmp(void *elem1,void *elem2);
    
    
    /*
     *   void *base    代表着元素存储的基地址
     *   int  length   代表着数组长度 
     *   int  elemSize 代表着每个元素之间的间隔（即单个元素的存储大小）
     */
    void* linearSearch(void *key, void *base, int length, 
                        int elemSize,int (*cmpfun)(void *,void*))
    {
        for (int i = 0; i < length;++i)
        {
            void *elemAddr = (char *)base + i * elemSize;

            if (cmpfun(key,elemAddr) == 0)
            {
                return elemAddr;
            }
        }
        return NULL;
    }

{% endhighlight %}

上面的 **linearSearch** 函数就是泛型的线性查找实现。   
首先是传递指向一组数据的指针 （指向一组数组，base 一般情况下base 指向数组的第一个元素,  数组移动的位置都基于此处 ）。

其中:

{% highlight c %}

    void *elemAddr = (char *)base + i * elemSize;

{% endhighlight %}
    
这个是查找数据的小技巧。 由于是泛型的元素值， 所以 elemSize 的大小是传递的数据类型大小。   
而`` i `` 是每次移动的指针长度。 最后的移动准则是基于`` char *`` 

每次一移动一个指针位（由于不同类型的数组单元长度是不同的， 所以进行强制类型转换）。  

{% highlight c %}

    (char *  ) base + i * elemSize ;

{% endhighlight %}

例如， 在32 位机器上查找int 类型的数组时，每次移动的一个数组元素位，（移动 4 个字节）

声明一个指针函数用于比较大小。

{% highlight c %}

    int intCmp(void *elem1,void *elem2);

{% endhighlight %}

这里做一个简单的比较实现..

{% highlight c %}

    int intCmp(void *elem1,void *elem2)
    {
        int *iPo1 = (int *)elem1;
        int *iPo2 = (int *)elem2;

        return *iPo1 - *iPo2; 
    }

{% endhighlight %}
    
（上面的代码是基于 函数指针cmpFunc 参数做的一个范例，其他的数据操作，根据实际情况而定）
 测试linearSearch 


{% highlight c %}

    //对于一个数据是 int 类型的实例
    int main(int argc, const char * argv[])
    {
        int elemArray[] = {1,2,4,6,4,7};
        int size = 6;
        int number = 7;

        void *iFound = linearSearch(&number, elemArray, size, sizeof(int), intCmp);

        if (iFound == NULL)
        {
            printf("Not Found -_-||\n");
        }
        else
        {
            printf("Find it !\n");
        }

        printf("iFound = %d\n",iFound);
        printf("&iFound = %d\n",&iFound);
        printf("&elemArray[5] = %d\n",&elemArray[5]);

        system("pause");
        return 0;
    }   


{% endhighlight %}

注意参数的 elemSize 接收的是 sizeof(int) ， 而其中的 intCmp 函数指针是 cmpFunc 其中的一个参数

运行效果：
    
{% highlight console %}

    ^-^
    iFound = 1338550980
    &iFound = 1338550936
    &elemArray[5] = 1338550980
    
{% endhighlight %}
                        
                        
---
layout: post
title:  "数据结构-栈 C泛型实现"
date:   2017-06-19 20:27:35 +0800
categories: jekyll update
---

# 数据结构-栈 C泛型实现

首先说到栈，大家最基本能够想到的就是经典的数据结构之一。它拥有 **`后进先出`** 的特性, 在程序设计运用当中最为广泛。下面来说下这个数据结构的实现。

由于是基本的数据结构，打算用 **`C`** 来完整的实现该结构， 实现过程中会用到 **`泛型 `**。 (很多教科书中没有涉及到泛型种概念。）

**准确地讲，也就是C语言在数据结构当中真正的实现 ,而非教材中的 `伪代码`**。

- 结构声明 : stack.h

{% highlight c %}

  #ifndef _STACK_H
  #define _STACK_H
  //  定义栈的通用类型 数据结构  
  //  使用线性表的存储结构思想，在数据模型中记录存储容量，和数据实际存储容量
  
  #define STACKSTORAGE 100   //栈的存储容量
  #define TIMES        2     //栈的扩充容量
  
  typedef struct {
    void *elems;           //栈模型的堆区存储结构数据地址
    int  stack_h;          //数据栈的存储容量
    int  virStack_h;       //数据栈的实际存储容量
    
    int  elemSize;         //单位数据类型所占的空间
  }Stack;
  
  //  初始化栈数据模型
  //  销毁栈数据模型
  void initialStack(Stack *S,int elemSize);
  void disposeStack(Stack *S);

  //  入栈操作函数
  //  出栈操作函数
  void StackPush(Stack *S,void *elem_addr_ptr);
  void StackPop(Stack *S,void *elem_addr_ptr);

  //  判断栈数据模型是否为空
  int Stack_not_empty(Stack *S);

  //   增加数据栈模型容量
  static void Stackincrease_H(Stack *S);
  #endif

{% endhighlight %}  


基本上从内容来看和教科书中的没太大的差别。直接在结构体的定义当中 使用了 **`typedef`** 简化了定义的结构。

首先是初始化栈存储空间的函数

{% highlight c %}

  void initialStack(Stack *S,int elemSize)

{% endhighlight %} 

没有任何的返回值，参数 Stack *S 结构体的类型的指针,  int elemSize 数据类型的大小
其中没有太多的操作:

{% highlight c %}

  // 泛型的操作基准是，要得到元素数据类型的值.
  S->elemSize = elemSize; 
  // 栈的目前的存储容量
  S->stack_h = STACKSTORAGE;

{% endhighlight %} 


然后手动申请堆区的的空间

{% highlight c %}

    S->elems = malloc(S->stack_h * elemSize);

{% endhighlight %} 

栈空间的销毁操作这里不做说明，这里要说的是栈的推入（压栈）操作。

{% highlight c %}

  void StackPush(Stack *S,void *elem_add_ptr)

{% endhighlight %} 

参数 elem_add_ptr 是通用类型的指针，可以接受任意数据类型的指针，这里的判断实际存储容量和标明的存储容量，是记录在结构体成员当中的两个值。这里需要扩充栈的容量

{% highlight c %}

    if(S->stack_h == S->virStack_h)
    {
        //栈容量已满，需要扩充
        Stackincrease_H(S);
    }

{% endhighlight %} 

接下来是指针的操作

{% highlight c %}
  
  void *stack_top = (char *)S->elems + S->virStack_h*S->elemSize;

{% endhighlight %}   

这里的操作非常难以理解。可以把它划分为三部：

{% highlight c %}

   void *stack_top = S->elems;

{% endhighlight %} 

也就是一个通用类型的指针存储了一个 结构中指向堆区的一块空间。

接下来是实际存储的容量记录在 S->virStack_h 中，此时如果是泛型的元素的话，只需要将``每个元素单元``的大小和栈中的``存储容量大小``相乘，便得到了栈实际存储空间的起始位置！ （例如 存储的是 `` int `` 类型的数据 则该单元是 4 并且，起始位置(栈顶位置）是当前指针 S->elems 偏移  **``S->virStack_h * S->elemSize ``**的量大小。

但是又不能确定该怎么偏移，**``所以把他变为偏移的基本单位量 1 字节 （即 char 类型）``**, 这时便做出了强制类型转换。 

{% highlight c %}

  (char *) S->elems  // 是单位偏移的游标位。

{% endhighlight %} 

 偏移量是
 
{% highlight c %}

  S->virStack_h * S->elemSize

{% endhighlight %} 

所以就出现了这样的表达式：

{% highlight c %}

  void *stack_top = (char *)S->elems + S->virStack_h*S->elemSize;

{% endhighlight %} 

紧接的一步是调用 memcpy 这个函数 

{% highlight c %}

   void memcpy (void *startAddress, void *endAddress, int CopySize)

{% endhighlight %} 

上面是函数的原型

{% highlight c %}

  memcpy(stack_top,elem_add_ptr,S->elemSize);

{% endhighlight %}   

我们把top指针的位置和插入元素的指针传做一次运算，发生一次内存的拷贝，拷贝的大小是元素的数据类型大小。

- 数据结构操作的实现

{% highlight c %}

  #include "stack.h"
  
  #include <stdlib.h>
  #include <assert.h>
  #include <string.h>
  
  
  void initialStack(Stack *S,int elemSize)
  {
      S->elemSize = elemSize;
      // 栈的目前的存储容量
      S->stack_h = STACKSTORAGE;
      // 栈当前的存储的元素初始化为 0
      S->virStack_h = 0;
      S->elems = malloc(S->stack_h * elemSize);

      assert(S->elems != NULL);
  }
  
  // 销毁栈空间
  void disposeStack(Stack *S)
  {
      if(S->elems != NULL)
      {
          free(S->elems);
      }
  }
  
  // 扩充栈容量操作，默认是按照定义的结构体 完成翻倍扩充
  static void Stackincrease_H(Stack *S)
  {
      S->stack_h *= TIMES;
      S->elems  = realloc(S->elems,S->stack_h*S->elemSize);
  }

  // 检查栈存储的量是否为空
  int Stack_not_empty(Stack *S)
  {
      return S->virStack_h;
  }
  
  void StackPush(Stack *S,void *elem_add_ptr)
  {
      if(S->stack_h == S->virStack_h)
      {
          //栈容量已满，需要扩充
          Stackincrease_H(S);
      }
      // 运用 void 类型的指针接受任意类型数据与实际栈容量变化的特点
      // 使指向栈顶元素的通用类型指针偏移变化，始终偏移至栈顶（char * 偏移1字节）
      void *stack_top = (char *)S->elems + S->virStack_h*S->elemSize;
      
      // 调用内存块复制函数，将所要入栈元素内存块完全复制至站栈的数据模型 
      // void memcpy (void *startAddress, void *endAddress, int CopySize)
      memcpy(stack_top,elem_add_ptr,S->elemSize);

      S->virStack_h++;
  }
  
  
  void StackPop(Stack *S,void *elem_addr_ptr)
  {
      if(Stack_not_empty(S))
      {
          void *stack_top = (char *)S->elems + (S->virStack_h-1)*S->elemSize;
          //原理与 StackPush 中的 stack_top 相同    
          // 栈存储容量在渐变中减 1
          memcpy(elem_addr_ptr,stack_top,S->elemSize);

          S->virStack_h--;
      }
  }

{% endhighlight %} 
  
以上的部分是 栈的数据结构实现，并且是 **`C的泛型实现`** ，很多情况下大多数的同学比较难以理解。   
在上述的 StackPop, StackPush 操作中，大量使用了 void 型的指针变量。

> void * 被译为通用类型的指针，可以接收其他任何的指针类型.

因为该类型指针的特性， 所以存储数据中也就完成了泛型的操作。

下面是测试数据结构的代码：

{% highlight c %}

  Stack stack_char;
  Stack stack_double;

{% endhighlight %} 

注意的是在使用结构体，直接在栈区声明即可，

{% highlight c %}

  char char_test[20],char_received[20];
  double double_test[20],double_received[20];

  initialStack(&stack_char,sizeof(char));
  initialStack(&stack_double,sizeof(double));

{% endhighlight %} 
  
直接初始化了两个不同数据类型的栈

{% highlight c %}


  for (int i = 0; i < 20; i++)
  {
    char_test[i] = i + 65;
    double_test[i] = i+1.005004;
  }

  for(int i = 0;i < 20;i++)
  {
      StackPush(&stack_char,&char_test[i]);
      StackPush(&stack_double,&double_test[i]);
  }

  for (int i = 0; i < 20; i++)
  {
    if (Stack_not_empty(&stack_char) &&
      Stack_not_empty(&stack_double) )
    {
        StackPop(&stack_char,&char_received[i]);
        StackPop(&stack_double,&double_received[i]);
    }
  }

  for(int i = 0;i < 20;i++)
  {
    cout<<char_received[i]<<"     "<<double_received[i]<<endl;
  }

{% endhighlight %} 

                        
                        



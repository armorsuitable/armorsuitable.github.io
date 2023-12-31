# Seq, Array

## Arrays


基于 0 的索引访问元素


```scala
scala> val fiveInts = new Array[Int](5)
val fiveInts: Array[Int] = Array(0, 0, 0, 0, 0)
```

设置元素数据

```scala
scala> fiveInts(1) = 13

scala> fiveInts
val res3: Array[Int] = Array(0, 13, 0, 0, 0)
```


## ListBuffer

list 类提供头部访问，非尾部访问的快速方式
ListBuffer 提供了元素前缀添加，后缀元素添加的方法  +=： ， +=

```scala
scala> val buf = new ListBuffer[Int]
val buf: scala.collection.mutable.ListBuffer[Int] = ListBuffer()

scala> buf += 1
val res4: buf.type = ListBuffer(1)

scala> buf += 2
val res5: buf.type = ListBuffer(1, 2)

scala> 3 +=: buf
val res6: buf.type = ListBuffer(3, 1, 2)
```

ListBuffer 转换为 List 的

```scala
scala> buf.toList
val res7: List[Int] = List(3, 1, 2)

scala> val list = buf.toList
val list: List[Int] = List(3, 1, 2)
```

ListBuffer 访问

```scala
scala> buf.length

res12: Int = 2

scala> buf(0)
res13: Int = 12
```


### Strings

```scala
scala> def hasUpperCase(s: String) = s.exists(_.isUpper)
hasUpperCase: (s: String)Boolean

scala> hasUpperCase("Robert Frost")
res14: Boolean = true

scala> hasUpperCase("e e cummings")
res15: Boolean = false
```

## Operation Of List

所有的List 都由两个块组成 **Nil** 和 **::**   (称之为 cons)


**Nil  表明是一个空的List**  

```scala
val fruit = "apples" :: ("oranges" :: ("pears" :: Nil))
val nums  = 1 :: (2 :: (3 :: (4 :: Nil)))
```


所有的List 都存在的基本操作

**head,  tail,  isEmpty**

```bash
scala>Nil.isEmpty
val res21: Boolean = false


scala> Nil.tail
java.util.NoSuchElementException: head of empty list
  at scala.collection.immutable.Nil$.head(List.scala:662)
  ... 32 elided
```


向List 添加一个元素(头部)

```scala
val elemList = "stringElemen" :: Nil

// 此时的 elemList 类型为 List[String]

val anyList = 1 :: elemList  

// 此时的 anyList 为 List[Any] , size  = 2 , head = 1, tail = List(stringElemen)


```

向List 尾部添加一个元素

```scala
scala> List(1,2,3) :+ 4

scala> Results in List[Int] = List(1, 2, 3, 4)
```


## First-Order  （List  一阶函数方法）

### 1. Concatenating two lists 

```scala
scala> List(1, 2) ::: List(3, 4, 5)
res0: List[Int] = List(1, 2, 3, 4, 5)

scala> List() ::: List(1, 2, 3)
res1: List[Int] = List(1, 2, 3)

scala> List(1, 2, 3) ::: List(4)
res2: List[Int] = List(1, 2, 3, 4)
```

###  2. two method recommended techiques

####  2. 1访问列表尾部的 init,  last 

init 返回除了最后一个元素之外的余下列表

```scala
scala> val abcde = List('a', 'b', 'c' , 'd' ,'e')
val abcde: List[Char] = List(a, b, c, d, e)

scala> abcde.init
val res49: List[Char] = List(a, b, c, d)
```

last 返回最后一个元素

```scala
scala> abcde.last
val res50: Char = e
```

### 3. 获取list 的范围列表

```scala
scala> abcde
val res53: List[Char] = List(a, b, c, d, e)

scala> abcde.indices
val res54: scala.collection.immutable.Range = Range 0 until 5
```

### 4. flatten List 为一个 list

```scala
scala> val ct = List(List(1,3,4,5), List(2,4, 6, 9)).flatten
val ct: List[Int] = List(1, 3, 4, 5, 2, 4, 6, 9)
```


### 5. List 元素连接器

```scala
scala> ct.mkString("#", "|", "#")
val res57: String = #1|3|4|5|2|4|6|9#
```


## High-Order (高阶方法)

### Mapping over lists (map, flatMap, foreach)

```scala
scala> val words = List("Car", "fox", "quick", "the")
val words: List[String] = List(Car, fox, quick, the)


scala> words.map(i => i.toList)
val res71: List[List[Char]] = List(List(C, a, r), List(f, o, x), List(q, u, i, c, k), List(t, h, e))


scala> words.map(i => i.reverse)
val res70: List[String] = List(raC, xof, kciuq, eht)
```






# Map, Set 

## HashMap

```scala
scala> val mapping = mutable.Map.empty[String, Int]
val mapping: scala.collection.mutable.Map[String,Int] = HashMap()

scala> mapping("hellocc") = 1

scala> mapping("byecc") = 0

scala> mapping
val res8: scala.collection.mutable.Map[String,Int] = HashMap(byecc -> 0, hellocc -> 1)

scala>mapping += ("hello bro" -> 1035)
val res11: mapping.type = HashMap(byecc -> 0, hellocc -> 1, hello bro -> 1035)
```


initialize the TreeMap

```scala

scala> import scala.collection.immutable.TreeMap
import scala.collection.immutable.TreeMap

scala> val tm = TreeMap()
                       ^
       error: diverging implicit expansion for type scala.math.Ordering[K]
       starting with method Tuple9 in object Ordering

scala> val tm = TreeMap.empty[Char, Int]
val tm: scala.collection.immutable.TreeMap[Char,Int] = TreeMap()
```

## Set

```scala
scala> import scala.collection.mutable
import scala.collection.mutable

scala> val mutableSet = mutable.Set(1, 40, 30)
val mutableSet: scala.collection.mutable.Set[Int] = HashSet(40, 1, 30)

scala> mutableSet += 35
val res2: mutableSet.type = HashSet(40, 1, 35, 30)

scala> mutableSet += 315
val res3: mutableSet.type = HashSet(40, 1, 35, 315, 30)

scala>

scala> mutableSet.contains(315)
val res4: Boolean = true
```

to Array , TreeSet or List

```scala
scala> import scala.collection.mutable.TreeSet
import scala.collection.mutable.TreeSet

scala> mutableSet to TreeSet
val res18: scala.collection.mutable.TreeSet[Int] = TreeSet(1, 30, 35, 40, 315)

scala> val ts = mutableSet to TreeSet
val ts: scala.collection.mutable.TreeSet[Int] = TreeSet(1, 30, 35, 40, 315)

scala> ts.head
val res19: Int = 1

scala> ts.last
val res20: Int = 315


scala> val l = mutableSet to List
val l: List[Int] = List(40, 1, 35, 315, 30)

scala> mutableSet to Array
val res23: Array[Int] = Array(40, 1, 35, 315, 30)
```


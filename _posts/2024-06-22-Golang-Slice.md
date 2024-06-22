# Golang Slice 

**Expending a slice**

A Diagram Showing


|  ptr  | len         |  cap  | 
|:-:|:-:|:-:|     
|  代表Slice 指针起始位置  | 当前slice 的长度         |  代表slice 能够扩展的位置  | 

    ptr                   len    cap
    |                      |      |
    |                      |      |
    |        [   x  ]      |      |
    ------>  [ elem ]      |      |
             [ elem ]      |      |
             [ elem ]      |      |
             [ elem ]      |      |
             [ elem ]  <----      | 
             [  *   ]             |
             [  *   ]             |
             [  *   ]             |
             [  *   ]  <----------      


**Demo Code**             

```go
arr := [8]int{0, 5, 10, 15, 20, 25, 30, 35}
s := arr[2:6]
s1 := s[3:5]
```

output
```
s:
[10, 15, 20, 25]
s1:
[25, 30]
```
Slice 始终是**底层数组的一个View** ,多个Slice 指向同一个数组空间，其中x 空间代表不可见，elem 代表slice 可以访问的元素， * 代表不超过cap可以**向后**扩展的元素

注意📢：Slice 只能向后扩展元素，即： slice[index] 中的Index 不可以超越 cap(Slice)

```go
arr := [7]int{0, 5, 10, 15, 20, 25, 30}
s := arr[2:6]
s1 := s[3:5]

fmt.Printf("len(s) = %d , cap(s) = %d", len(s), cap(s))
fmt.Printf("len(s1) = %d , cap(s1) = %d", len(s1), cap(s1))
```

output
```
len(s) = 4 , cap(s) = 6
len(s1) = 2 , cap(s1) = 3
```

**Expending Slice & Slice view different Array**

```go
s = append(s, 10)
s1 = append(s, 11)
s1 = append(s1, 12)
fmt.Printf("s = %v\n", s)
fmt.Printf("s1 = %v\n", s1)
fmt.Printf("arr = %v\n", arr)
```

s1 **no longer** view arr

output
```
s = [10 15 20 25 10]
s1 = [10 15 20 25 10 11 12]
arr = [0 5 10 15 20 25 10 11]
```
当添加元素超过 cap(slice) 之后，系统会重新分配底层空间更大的数组，原先的数组可能会被GC处理掉


**Copy Slice And Delete Slice elements**

```go
// declare a 8 element slice
a1 := []int{1, 3, 5}
s2 := make([]int, 8, 12)
fmt.Printf("s2 = %v, cap(s2) =  %d\n", s2, cap(s2))
copy(s2, a1)
fmt.Printf("after copy s2 = %v, len(s2) = %d, cap(s2) =  %d\n", s2, cap(s2))

// delete element from slice
s2 = append(s2[:2], s2[3:]...)
fmt.Printf("s2 = %v, len(s2) = %d, cap(s) = %d\n", s2, len(s2), cap(s2))
```

output
```
s2 = [0 0 0 0 0 0 0 0], cap(s2) =  12
after copy s2 = [1 3 5 0 0 0 0 0], len(s2) = 8, cap(s2) =  12
after delete s2 = [1 3 0 0 0 0 0], len(s2) = 7, cap(s) = 12
```

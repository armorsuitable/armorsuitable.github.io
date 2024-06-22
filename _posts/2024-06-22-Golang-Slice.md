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

---
layout: post
title:  "golang--sort包中的Interface"
date:   2015-04-28 09:56:20
categories:   golang
---

golang的sort包中定义了一个名为Interface的interface，还有一个Sort(Interface)的函数：

``` go
type Interface interface {
        // Len is the number of elements in the collection.
        Len() int
        // Less reports whether the element with
        // index i should sort before the element with index j.
        Less(i, j int) bool
        // Swap swaps the elements with indexes i and j.
        Swap(i, j int)
}

func Sort(data Interface)
```

所以，只要一个类型实现了Interface接口，都可以调用Sort函数进行排序，在某些方面用起来感觉比c++中的sort()函数要更直观一点。

放一个官方的例子, ByAge实现了Interface，这样就可以根据自己规定的排序方式，来对ByAge进行排序：

``` go
package main

import (
        "fmt"
        "sort"
)

type Person struct {
        Name string
        Age  int
}

func (p Person) String() string {
        return fmt.Sprintf("%s: %d", p.Name, p.Age)
}

// ByAge implements sort.Interface for []Person based on
// the Age field.
type ByAge []Person

func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }

func main() {
        people := []Person{
                {"Bob", 31},
                {"John", 42},
                {"Michael", 17},
                {"Jenny", 26},
        }

        fmt.Println(people)
        sort.Sort(ByAge(people))
        fmt.Println(people)

}

```
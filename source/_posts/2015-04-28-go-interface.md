---
layout: post
title:  "golang中interface需要注意的一点地方"
date:   2015-04-28 09:56:20
categories:   golang
---

先看代码：

``` go
package main

import (
        "fmt"
)

type List []int
func (l List) Len() int { return len(l) }
func (l *List) Append(val int) { *l = append(*l, val) }

type Appender interface {
        Append(int)
}

func CountInto(a Appender, start, end int) {
        for i := start; i <= end; i++ {
                a.Append(i)
        }
}

type Lener interface {
        Len() int
}

func LongEnough(l Lener) bool {
        return l.Len()*10 > 42
}

func main() {
        var lst List
        // compiler error:
        // cannot use lst (type List) as type Appender in function argument:
        // List does not implement Appender (Append method requires pointer receiver)
        // INVALID: Append has a pointer receiver
        // CountInto(lst, 1, 10) 

        if LongEnough(lst) {  // VALID: Identical receiver type
                fmt.Printf(" - lst is long enough")
        }

        // A pointer value
        plst := new(List)
        CountInto(plst, 1, 10) // VALID: Identical receiver type
        if LongEnough(plst) {  // VALID: a *List can be dereferenced for the receiver
                fmt.Printf(" - plst is long enough")  //  - plst is long enoug
        }      
}

```

在上面的例子中，`CountInto(1st, 1, 10)`这一句编译时会报错，因为List实现Appender接口时的receiver是指针类型，所以调用时也必须使用指针类型。

所以interface这和struct自身的method调用时不同，interface使用时receiver的类型必须和实现时的类型完全一样，而struct调用自身的method时会自动转换类型。
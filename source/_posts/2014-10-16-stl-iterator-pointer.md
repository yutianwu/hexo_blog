---
layout: post
title:  "iterator和指针"
date:   2014-10-16 08:34:20
categories:   cpp
---

##Iterator

iterator一般用在容器中，下面是所有容器都支持的一些运算：
```
	*iter 返回迭代器 iter 所指向的元素的引用

	iter->mem 对 iter 进行解引用，获取指定元素中名为 mem 的成员。等效于 (*iter).mem

	++iter iter++ 给 iter 加 1，使其指向容器里的下一个元素

	--iter iter-- 给 iter 减 1，使其指向容器里的前一个元素

	iter1 == iter2 
	iter1 != iter2  比较两个迭代器是否相等（或不等）。当两个迭代器指向同一个容器中的同一个元素，或者当它们都指向同一个容器的超出末端的下一位置时，两个迭代器相等
```

在支持随机访问的两个容器vector和deque中，还支持另外一些更加灵活地运算：
```
	iter + n 
	iter - n  n，将产生指向容器中前面（后面）第 n 个元素的迭代器。新计算出来的迭代器必须指向容器中的元素或超出容器末端的下一位置

	iter1 += iter2 
	iter1 -= iter2 这里迭代器加减法的复合赋值运算：将 iter1 加上或减去 iter2 的运算结果赋给 iter1

	iter1 - iter2 两个迭代器的减法，其运算结果加上右边的迭代器即得左边的迭代器。这两个迭代器必须指向同一个容器中的元素或超出容器末端的下一位置

	>, >=, <, <= 迭代器的关系操作符。当一个迭代器指向的元素在容器中位于另一个迭代器指向的元素之前，则前一个迭代器小于后一个迭代器。关系操作符的两个迭代器必须指向同一个容器中的元素或超出容器末端的下一位置
```

C++ 语言使用一对迭代器标记迭代器范围（iterator range），这两个迭代器分别指向同一个容器中的两个元素或超出末端的下一位置，通常将它们命名为 first 和 last，或 beg 和 end，用于标记容器中的一段元素范围。

尽管 last 和 end 这两个名字很常见，但是它们却容易引起误解。其实第二个迭代器从来都不是指向元素范围的最后一个元素，而是指向最后一个元素的下一位置。该范围内的元素包括迭代器 first 指向的元素，以及从 first 开始一直到迭代器 last 指向的位置之前的所有元素。如果两个迭代器相等，则迭代器范围为空。

此类元素范围称为左闭合区间（left-inclusive interval），其标准表示方式为：

```
	[ first, last )
```

表示范围从 first 开始，到 last 结束，但不包括 last。迭代器 last 可以等于 first，或者指向 first 标记的元素后面的某个元素，但绝对不能指向 first 标记的元素前面的元素。

使用左闭右开空间有几个好处：

+ 当 first 与 last 相等时，迭代器范围为空；
+ 当 first 与不相等时，迭代器范围内至少有一个元素，而且 first 指向该区间中的第一元素。此外，通过若干次自增运算可以使 first 的值不断增大，直到 first == last 为止。

这样的话，就可以安全地写循环了：
```
	while (first != last) { //安全地写!=，而不用怕越界
         // safe to use *first because we know there is at least one element
         ++first;
     }
```

 这样的话，iterator看起来跟指针也没什么大的区别。。。。。

 下面看看algorithm中fill函数
```
	template <class ForwardIterator, class T> 
	fill (ForwardIterator first, ForwardIterator last, const T& val);
```
它及支持容器中的迭代器，但同时也支持普通数组中的指针：

``` cpp
// fill algorithm example
#include <iostream>     // cout
#include <algorithm>    // fill
#include <vector>       // vector
using namespace std;

int main () {
    vector<int> myvector (8);                       // myvector: 0 0 0 0 0 0 0 0
    
    //iterator
    fill (myvector.begin(),myvector.begin()+4,5);   // myvector: 5 5 5 5 0 0 0 0
    fill (myvector.begin()+3,myvector.end()-2,8);   // myvector: 5 5 5 8 8 8 0 0
    
    cout << "myvector contains:";
    for (vector<int>::iterator it=myvector.begin(); it!=myvector.end(); ++it)
        cout << ' ' << *it;
    cout << '\n';
    
    int a[10];
    //指针
    fill(a, a + 10, 10);
    for (int i = 0; i < 10; i++) {
        cout<<a[i]<<"\t";
    }
    
    return 0;
}
```
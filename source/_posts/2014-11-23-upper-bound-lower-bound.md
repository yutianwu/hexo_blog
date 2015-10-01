---
layout: post
title:  "STL: upper_bound & lower_bound"
date:   2014-11-23 11:39:20
categories:   cpp
tags: [二分搜索]
---

有时候，我们用到二分搜索的时候需要求出大于等于一个数的最小值和小于等于一个数的最大值，这个时候我们除了自己写出二分搜索的代码外，还可以使用这两个函数。

``` cpp
// lower_bound/upper_bound example
#include <iostream>     // std::cout
#include <algorithm>    // std::lower_bound, std::upper_bound, std::sort
#include <vector>       // std::vector

int main () {
  int myints[] = {10,20,30,30,20,10,10,20};
  std::vector<int> v(myints,myints+8);           // 10 20 30 30 20 10 10 20

  std::sort (v.begin(), v.end());                // 10 10 10 20 20 20 30 30

  std::vector<int>::iterator low,up;
  low=std::lower_bound (v.begin(), v.end(), 20); //          ^
  up= std::upper_bound (v.begin(), v.end(), 20); //                   ^

  std::cout << "lower_bound at position " << (low- v.begin()) << '\n';
  std::cout << "upper_bound at position " << (up - v.begin()) << '\n';

  return 0;
}
```
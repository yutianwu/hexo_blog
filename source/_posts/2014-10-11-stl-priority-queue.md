---
layout: post
title:  "STL中的priority_queue"
date:   2014-10-11 22:18:20
categories:   cpp
---

如果碰到需要使用优先级序列的时候，可以不用自己去实现，使用STL中的便是，
STL其它还提供了许多有用的工具，要善加利用。

下面是C++ Reference中priority_queue的使用的例子，知道这些应该就够了:

```
	template <class T, class Container = vector<T>,
  class Compare = less<typename Container::value_type> > class priority_queue;
```

``` cpp
// constructing priority queues
#include <iostream>       // std::cout
#include <queue>          // std::priority_queue
#include <vector>         // std::vector
#include <functional>     // std::greater

class mycomparison
{
  bool reverse;
public:
  mycomparison(const bool& revparam=false)
    {reverse=revparam;}
  bool operator() (const int& lhs, const int&rhs) const
  {
    if (reverse) return (lhs>rhs);
    else return (lhs<rhs);
  }
};

int main ()
{
  int myints[]= {10,60,50,20};

  std::priority_queue<int> first;
  std::priority_queue<int> second (myints,myints+4);
  //需要注意的是下面这个，priority_queue默认的是使用less<int>,
  //所以生成的都是大头堆，如果需要生成小头堆得话，比较函数得使用greater<int>
  std::priority_queue<int, std::vector<int>, std::greater<int> >
                            third (myints,myints+4);
  //如果碰到了需要比较自定义的类型，可以参照下面这个方法来实现
  typedef std::priority_queue<int,std::vector<int>,mycomparison> mypq_type;

  mypq_type fourth;                       // less-than comparison
  mypq_type fifth (mycomparison(true));   // greater-than comparison

  return 0;
}
```
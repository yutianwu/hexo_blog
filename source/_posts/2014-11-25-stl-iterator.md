---
layout: post
title:  "STL:iterator的一些常见操作"
date:   2014-11-25 10:20:20
categories:   cpp
---

有时在做题时，碰到vector之类的容器时，如果我要制定一个容器的范围，那么我就不得不声明三个形参，一个vector，两个下标。但是在这里我们还可以实用迭代器来实现，这样，就只需要实用两个形参就可以了。

iterator在实际题目中的使用见重建二叉树部分。

下面，举出一些iterator的常用函数：

+ begin(), end()

``` cpp
#include <iostream>     // std::cout
#include <vector>       // std::vector, std::begin, std::end

int main () {
  int foo[] = {a};
  std::vector<int> bar;

  // iterate foo: inserting into bar
  //数组也可以使用
  for (auto it = std::begin(foo); it!=std::end(foo); ++it)
    bar.push_back(*it);

  // iterate bar: print contents:
  std::cout << "bar contains:";
  for (auto it = std::begin(bar); it!=std::end(bar); ++it)
    std::cout << ' ' << *it;
  std::cout << '\n';

  return 0;
}
```

+ distance()

``` cpp
// advance example
#include <iostream>     // std::cout
#include <iterator>     // std::distance
#include <list>         // std::list

int main () {
  std::list<int> mylist;
  for (int i=0; i<10; i++) mylist.push_back (i*10);

  std::list<int>::iterator first = mylist.begin();
  std::list<int>::iterator last = mylist.end();

  std::cout << "The distance is: " << std::distance(first,last) << '\n';

  return 0;
}
```

+ next()

``` cpp
// next example
#include <iostream>     // std::cout
#include <iterator>     // std::next
#include <list>         // std::list
#include <algorithm>    // std::for_each

int main () {
  std::list<int> mylist;
  for (int i=0; i<10; i++) mylist.push_back (i*10);

  std::cout << "mylist:";
  std::for_each (mylist.begin(),
                 std::next(mylist.begin(),5),
                 [](int x) {std::cout << ' ' << x;} );

  std::cout << '\n';

  return 0;
}
```

+ pre()

``` cpp
// prev example
#include <iostream>     // std::cout
#include <iterator>     // std::next
#include <list>         // std::list
#include <algorithm>    // std::for_each

int main () {
  std::list<int> mylist;
  for (int i=0; i<10; i++) mylist.push_back (i*10);

  std::cout << "The last element is " << *std::prev(mylist.end()) << '\n';

  return 0;
}
```

+ reverse_iterator()

``` cpp
// reverse_iterator example
#include <iostream>     // std::cout
#include <iterator>     // std::reverse_iterator
#include <vector>       // std::vector

int main () {
  std::vector<int> myvector;
  for (int i=0; i<10; i++) myvector.push_back(i);

  typedef std::vector<int>::iterator iter_type;
                                                         // ? 0 1 2 3 4 5 6 7 8 9 ?
  iter_type from (myvector.begin());                     //   ^
                                                         //         ------>
  iter_type until (myvector.end());                      //                       ^
                                                         //
  std::reverse_iterator<iter_type> rev_until (from);     // ^
                                                         //         <------
  std::reverse_iterator<iter_type> rev_from (until);     //                     ^

  std::cout << "myvector:";
  while (rev_from != rev_until)
    std::cout << ' ' << *rev_from++;
  std::cout << '\n';

  return 0;
}
```

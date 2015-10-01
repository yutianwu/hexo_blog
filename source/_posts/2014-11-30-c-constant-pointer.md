---
layout: post
title:  "常量指针和指针常量"
date:   2014-11-30 14:41:20
categories:   cpp
---

###常量指针
常量指针是说指针所指向的内容是常量。

比如:
``` cpp
char a[10] = "hello";
const char *p = a;
*p = 'a'; //这样是不行的，因为申明的是一个常量指针
```

###指针常量
指针常量说的是指针是一个常量，不能够更改指针的值。

比如：
``` cpp
char a[10] = "hello";
char b[10] = "world";
char * const p = &a;
p = &b; //这样是不行的，因为申明的是一个指针常量
```
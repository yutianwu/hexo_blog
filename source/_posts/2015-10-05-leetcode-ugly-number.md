---
layout: post
title:  "LeetCode: Ugly Number"
categories: LeetCode
date:   2015-10-05 22:09:20
tags: ["Math"]
---

> Write a program to check whether a given number is an ugly number.

> Ugly numbers are positive numbers whose prime factors only include 2, 3, 5. For example, 6, 8 are ugly while 14 is not ugly since it includes another prime factor 7.

> Note that 1 is typically treated as an ugly number.

数学题是最头疼的了，这题开始想到是用检查一个数是否为素数的方法，后面发现太麻烦，所以找到了以下的方法。希望以后不要遇到太多的数学题。。。好怕。。。

根据丑数的定义，丑数只能被2、3和5整除。也就是说如果一个数如果它能被2整除，我们把它连续除以2；如果能被3整除，就连续除以3；如果能被5整除，就除以连续5。如果最后我们得到的是1，那么这个数就是丑数，否则不是。此处需要注意0这个问题。

代码如下：
``` cpp
class Solution {
public:
    bool isUgly(int num) {
        if (num == 0) return false;
        while (num % 2 == 0) {
            num /= 2;
        }
        while (num % 3 == 0) {
            num /= 3;
        }
        while (num % 5 == 0) {
            num /= 5;
        }
        
        return num == 1 ? true : false;
    }
};
```


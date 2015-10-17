---
layout: post
title:  "LeetCode: Add Digits"
categories: LeetCode
date:   2015-09-30 21:49:20
tags: ["Math"]
---

> Given a non-negative integer num, repeatedly add all its digits until the result has only one digit.

> For example:

> Given num = 38, the process is like: 3 + 8 = 11, 1 + 1 = 2. Since 2 has only one digit, return it.

此题如果用一般的方法解决，很容易，代码如下：

``` cpp
class Solution {
public:
    int addDigits(int num) {
        while (num >= 10) {
            int tmp = 0;
            while (num != 0) {
                tmp += num % 10;
                num = num / 10;
            }
            num = tmp;
        }
        return num;
    }
};
```

但是题中还提到可以不用循环就可以解决，但一般这都属于一些比较技巧性的东西，见多就好，如果临时想起，必然是大神了。

下面记录一下从别处看来的解法：

假设输入的num是一个5位的数字，各位数分别为$a, b, c, d, e$。那么$num = (a+b+c+d+e)+(a\times9999+b\times999+c\times99+d\times9)$。

然后对$a+b+c+d+e$重复进行此操作，最后将得到$num=x+y$,其中$1<=x<=9$,$y$则是一个能被9整除的表达式。我们所需要的结果就是$x$。

而$x=(x-1)\%9 + 1$,（此处是因为$x$可能为9），亦即$x=(num-1)\%9+1$，（因为$y$能被9整除）。

所以答案就更简单了：
``` cpp
class Solution {
public:
    int addDigits(int num) {
        return (num - 1) % 9 + 1;
    }
};
```
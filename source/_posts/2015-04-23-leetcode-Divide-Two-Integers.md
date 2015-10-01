---
layout: post
title:  "Math: Divide Two Integers"
date:   2015-04-23 09:56:20
categories: problems
---

>Divide two integers without using multiplication, division and mod operator.
>
>If it is overflow, return MAX_INT.

``` cpp
/*
这里需要注意的就是溢出的问题，我们知道，int的范围为
-2147483648 ~ 2147483647
所以需要考虑的情况就是当-2147483648 / -1时，会溢出
*/
class Solution {
public:
    int divide(int dividend, int divisor) {
        long long a = dividend >= 0 ? dividend : -(long long) dividend;
        long long b = divisor >= 0 ? divisor : -(long long) divisor;
        
        long long result = 0;
        while (a >= b) {
            long long c = b;
            for (int i = 0; a >= c; ++i, c <<= 1) {
                a -= c;
                result += 1 << i;
            }
        }
        
        result = ((dividend ^ divisor) >> 31) ? -result : result;
        
        return result >= INT_MAX ? INT_MAX : result;
    }
};
```
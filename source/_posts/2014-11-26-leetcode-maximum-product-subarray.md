---
layout: post
title:  "动态规划: Maximum Product Subarray"
date:   2014-11-26 21:45:20
categories: problems
tags: [动态规划]
---

> Find the contiguous subarray within an array (containing at least one number) which has the largest product.
>
> For example, given the array `[2,3,-2,4]`,
>
>the contiguous subarray `[2,3]` has the largest product = `6`.


``` cpp
/*

f(k) = Largest product subarray, from index 0 up to k, but must multiply A[k].
g(k) = Smallest product subarray, from index 0 up to k, but must multiply A[k].

在f(k)和g(k)中，
f(k)是指从0~k,最后乘以A[k]的最大值，
g(k)是指从0~k,最后乘以A[k]的最小值.
所以这也是为什么f(k)，g(k)并不是全局的最大值和最小值，之所以一定要乘上A[k]，是因为
求的是连续的范围，只有这样，才能够往下继续遍历。

f(k) = max( f(k-1) * A[k], A[k], g(k-1) * A[k] )
g(k) = min( g(k-1) * A[k], A[k], f(k-1) * A[k] )

在递推公式中，如果f(k)为A[k]，表示从k重新开始。
比如{2,3,-2,4}，k=2时，f(k)=-2,表示从-2处重新开始计算最大值。


*/
class Solution {
public:
    int maxProduct(int A[], int n) {
        int min_tmp = A[0];
        int max_tmp = A[0];
        int result = max_tmp;
        
        for (int i = 1; i < n; i++) {
            int a = min_tmp * A[i];
            int b = max_tmp * A[i];
            min_tmp = min(min(a, b), A[i]);
            max_tmp = max(max(a, b), A[i]);
            result = max(result, max_tmp);
        }
        return result;
    }
};
```

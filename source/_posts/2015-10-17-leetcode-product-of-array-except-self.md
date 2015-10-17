---
layout: post
title:  "LeetCode: Product of Array Except Self"
categories: LeetCode
date:   2015-10-17 12:00:20
tags: ["Array"]
---

> Given an array of `n` integers where `n > 1`, `nums`, return an array output such that `output[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

> Solve it without division and in `O(n)`.

> For example, given `[1,2,3,4]`, return `[24,12,8,6]`.

思路就是扫描数组两遍，比如说我要求第`i`位的结果，那么我可以先求出来第`i`位之前和之后的数字的乘积，然后把这两个结果想乘就得到了结果。代码如下


``` cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        vector<int> result(nums.size(), 1);
        for (int i = 1; i < nums.size(); i++) {
            result[i] *= nums[i - 1] * result[i - 1];
        }
        int pre = 1;
        for (int i = nums.size() - 2; i >= 0; i--) {
            pre *= nums[i + 1];
            result[i] *= pre;
        }
        return result;
    }
};
```
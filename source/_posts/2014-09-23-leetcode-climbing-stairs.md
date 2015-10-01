---
layout: post
title:  "动态规划: Climbing Stairs"
date:   2014-09-23 23:01:20
categories: problems
tags: [动态规划]
---
>You are climbing a stair case. It takes n steps to reach to the top.<br>
Each time you can either climb 1 or 2 steps. In how many distinct ways can you 
climb to the top?

``` cpp
/*
	这个题应该是最简单的动态规划题了
	设dp[i]为爬i层楼梯的不同走法，则我们可以得到一个递推关系：
	dp[i] = dp[i-1] + dp[i-2], dp[0] = 1, dp[1] = 1
*/
class Solution {
public:
	//第一种方法使用了一个dp数组，算法的空间复杂度为O(N)
    int climbStairs_1(int n) {
        vector<int> dp(n + 1, 1);

        for (int i = 2; i <= n; i++) {
        	dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }

    //第二种方法就使用了两个变量，算法的空间复杂度为O(1)，
    //在动态规划的问题时，我们经常使用变量和滚动数组来减少空间复杂度，能够将O(n^2)的
    //空间复杂度降低为O(n)的空间复杂度，将O(n)的空间复杂度降为O(1)
    int climbStairs_2(int n) {
    	int pre = 0, cur = 1;
    	for (int i = 1; i <= n; i++) {
    	    int tmp = cur;
    		cur = cur + pre;
    		pre = tmp;
    	}
    	return cur;
    }
};
```
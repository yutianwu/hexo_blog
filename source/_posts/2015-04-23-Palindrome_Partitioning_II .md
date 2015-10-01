---
layout: post
title:  "动态规划: Palindrome Partitioning II "
date:   2015-04-23 11:38:20
categories: problems
tags: [动态规划]
---

>Given a string s, partition s such that every substring of the partition is a palindrome.
>
>Return the minimum cuts needed for a palindrome partitioning of s.
>
>For example, given s = "aab",
>Return 1 since the palindrome partitioning ["aa","b"] could be produced using 1 cut.

``` cpp
/*
这里用到了两个dp，
一个是判断字符串s[i][j]是否为回文，
一个是记录s[0..i]最小的切割数

经典题
*/

class Solution {
public:
    int minCut(string s) {
        int n = s.size();
        vector<vector<bool> > isPal(n, vector<bool>(n, false));
        vector<int> cut(n, 0);
        for (int j = 0; j < n; j++) {
            cut[j] = j;
            for (int i = 0; i <= j; i++) {
                //如果子串 s[i...j]是回文串
                if (s[i] == s[j] && (j - i <= 1 || isPal[i + 1][j - 1])) {
                    isPal[i][j] = true;
                    if (i > 0)
                        cut[j] = min(cut[j], cut[i - 1] + 1);
                    else
                        cut[j] = 0; //如果 s[0...j]是回文串，则说明不需要切割
                }
            }
        }
        return cut[n - 1];
    }
};
```
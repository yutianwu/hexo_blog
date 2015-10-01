---
layout: post
title:  "动态规划: Distinct Subsequences "
date:   2014-09-25 21:00:20
categories: problems
tags: [动态规划]
---

>Given a string S and a string T, count the number of distinct subsequences of T in S.<br>
A subsequence of a string is a new string which is formed from the original string by
deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, `"ACE"` is a subsequence of `"ABCDE"` while `"AEC"` is not).<br>
Here is an example:<br>
S = `"rabbbit"`, T = `"rabbit"`<br>
Return 3.

###1.DFS解法
这道题如果简单地想的话，可以使用DFS来解。但是使用DFS的话时间复杂度很高，可能会超时，比如这个题。代码如下。

``` cpp
class Solution {
public:
    string s;
    string t;
    int len_s;
    int len_t;
    
    int numDistinct(string S, string T) {
        s = S;
        t = T;
        len_s = s.size();
        len_t = t.size();
        
        if (len_s == 0 || len_t == 0) {
            return 0;
        }
        
        return rec(0, 0);
    }
    
    //i for pointer of T, j for pointer of S
    int rec(int i, int j) {
        int res = 0;
        if (i == len_t) {
            res = 1;
        } else if (j == len_s) {
            res = 0;
        } else if (t[i] == s[j]) {
            res = rec(i + 1, j + 1) + rec(i, j + 1);
        } else {
            res = rec(i, j + 1);
        }
        
        return res;
    }
};
```
###2.在上一种解法上使用动态规划
由于DFS的时间限制，我们可以在上一种方法上进行改进，加上一个备忘数组，使用动态规划来解，就可以显著地降低时间复杂度。代码如下：

```
class Solution {
public:
    string s;
    string t;
    int len_s;
    int len_t;
    vector<vector<int>> dp;
    
    int numDistinct(string S, string T) {
        s = S;
        t = T;
        len_s = s.size();
        len_t = t.size();
        
        if (len_s == 0 || len_t == 0) {
            return 0;
        }
        
        vector<int> row(len_s + 1, -1);
        for (int i = 0; i <= len_t; i++) {
            dp.push_back(row);
        }
        
        return rec(0, 0);
    }
    
    //i for pointer of T, j for pointer of S
    int rec(int i, int j) {
        if (dp[i][j] >= 0) {
            return dp[i][j];
        }
        
        int res = 0;
        if (i == len_t) {
            res = 1;
        } else if (j == len_s) {
            res = 0;
        } else if (t[i] == s[j]) {
            res = rec(i + 1, j + 1) + rec(i, j + 1);
        } else {
            res = rec(i, j + 1);
        }
        
        return dp[i][j] = res;
    }
};
```

####3.继续改进
虽然上一种方法使用动态规划能够通过，但是空间复杂度很高，所以要我们追求一种空间复杂度比较低的解法。

先给出递推关系：

设dp(i, j)表示T[0, j]在S[0, i]中出现的次数

1.如果T[j] != S[i]，dp(i, j) = dp(i - 1, j)

2.如果T[j] == S[i]，dp(i, j) = dp(i - 1, j - 1) + dp(i - 1, j)

简单地想我们就可以使用一个dp[i][j]的辅助数组来解决问题，但是仔细看看这个题的话，我们可以发现dp(i, j) 只和dp(i - 1, j - 1),dp(i - 1, j)有关，恰好是后一行的结果只和前一行的结果有关，所以我们就可以只声明一个一行的一维数组，即一个滚动数组来完成二维数组的功能。

``` cpp
class Solution {
public:
    int numDistinct(string S, string T) {
		vector<int> dp(T.size() + 1, 0);
		dp[0] = 1;
		for (int i = 0; i < S.size(); i++) {
			for (int j = T.size() - 1; j >= 0; j--) {
				dp[j + 1] += S[i] == T[j] ? dp[j] : 0;
			}
		}		    
		return dp[T.size()];
	}
};
```

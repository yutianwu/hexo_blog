---
layout: post
title:  "动态规划: Word Break"
date:   2014-09-23 15:31:20
categories: problems
tags: [动态规划]
---

>Given a string s and a dictionary of words dict, determine if s can be segmented into a space-separated sequence of one or more dictionary words.<br>
For example, given<br>
s = `"leetcode"`,<br>
dict = `["leet", "code"]`.<br>
Return `true` because `"leetcode"` can be segmented as `"leet code"`.

``` cpp
class Solution {
public:
    //这是题解上给的答案，比较简单
    //设状态为 f(i),表示s[0, j]是否可以分词，递推关系如下：
    //f(i) = any_of(f(j) && s[j + 1, i] ∈ dict), 0 ≤ j < i
    bool wordBreak(string s, unordered_set<string> &dict) {
        int len = s.size();
        vector<bool> f(len + 1, false);
        f[0] = true; //空字符串设置为true
        for (int i = 1; i <= len; i++) {
            for (int j = 0; j < i; j++) {
                if (f[j] && isInSet(s.substr(j, i - j))) {
                    f[i] = true;
                    break;
                }
            }
        }   
        return f[len];
    }

    //这个解法是我自己写的，思路与算法导论上矩阵链乘法上讲的思路一致
    //设dp[i][j]表示s[i, j]是否可以分词，那么，我们也可以得到一个递推关系:
    //dp[i][j] = any_of(dp[i][k] && dp[k + 1][j]) || s[i, j] ∈ dict
    //这样一个问题就分解为两个子问题
    //在这个问题上最有特点的应该是求dp[i][j]的方式，它是以j-i的长度从小到大的顺序
    //求的
    bool wordBreakMy(string s, unordered_set<string> &dict) {
        int len = (int) s.size();
        vector<bool> row(len, false);
        vector<vector<bool>> dp(len, row);
        for (int i = 0; i < len; i++) {
            string tmp = s.substr(i, 1);
            if (isInSet(tmp, dict)) {
                dp[i][i] = true;
            }
        }
        
        for (int l = 2; l <= len; l++) {
            for (int i = 0; i < len - l + 1; i++) {
                int j = i + l - 1;
                bool flag = isInSet(s.substr(i, l), dict);
                for (int k = i; k < j && !flag; k++) {
                    flag |= (dp[i][k] && dp[k + 1][j]);
                }
                dp[i][j] = flag;
            }
        }
        
        return dp[0][len - 1];
    }
    
    bool isInSet(const string &str, const unordered_set<string> &dict) {
        unordered_set<string>::const_iterator got = dict.find(str);
        if (got == dict.end()) {
            return false;
        } else {
            return true;
        }
    }
};
```
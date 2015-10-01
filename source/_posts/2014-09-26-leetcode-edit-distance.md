---
layout: post
title:  "动态规划: Edit Distance"
date:   2014-09-26 16:07:20
categories: problems
tags: [动态规划]
---

>Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)
>
>You have the following 3 operations permitted on a word:
>
>a) Insert a character<br>
>b) Delete a character<br>
>c) Replace a character<br>


编辑距离，又称Levenshtein距离，是指两个字串之间，由一个转成另一个所需的最少编辑操作次数。许可的编辑操作包括将一个字符替换成另一个字符，插入一个字符，删除一个字符。

例如将kitten一字转成sitting：

1. sitten （k→s）
2. sittin （e→i）
3. sitting （→g）

思路如下：
设dp[i][j]表示word1[0, i]与word2[0, j]的编辑距离
那么根据编辑操作的三种方式，我们有：

```
1.如果word1[i] == word2[j]
    dp[i][j] = dp[i - 1][j - 1]
2.如果word1[i] != word2[j]
    dp[i][j] = min (
        dp[i - 1][j],           //在word1中删除一个字符word1[i]
        dp[i][j - 1],           //在word1中插入一个字符word2[j]
        dp[i - 1][j - 1]  + 1   //替换word1[i] == word2[j]
    )
```

``` cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        const size_t len1 = word1.size();
        const size_t len2 = word2.size();

        int dp[len1 + 1][len2 + 1];
        for (int i = 0; i <= len1; i++) {
            dp[i][0] = i;
        }
        for (int j = 0; j <= len2; j++) {
            dp[0][j] = j;
        }

        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    int mn = min(dp[i - 1][j], dp[i][j - 1]); //删除
                    dp[i][j] = 1 + min(dp[i - 1][j - 1], mn);  //替换
                }
            }
        }
        return dp[len1][len2];
}
};
```
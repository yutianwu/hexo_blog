---
layout: post
title:  "动态规划: Decode Ways"
date:   2014-09-24 19:09:20
categories: problems
tags: [动态规划]
---

>A message containing letters from A-Z is being encoded to numbers using the >following mapping:<br>
`'A' -> 1` <br>
`'B' -> 2` <br>
`...` <br>
`'Z' -> 26`<br>
Given an encoded message containing digits, determine the total number of ways to decode it.<br>
For example,<br>
Given encoded message `"12"`, it could be decoded as `"AB"` (1 2) or `"L"` (12).<br>
The number of ways decoding `"12"` is 2.<br>

``` cpp
/*
这个题是Climing Stairs的升级版，在前面的基础上加了几个判断条件：
1.先判断边界条件，如果字符串为空或者s为“0”的话，结果为0
2.判断当前字符是否为“0”，前面一个字符和当前字符组合是否大于26

做这种题应当注意边界条件，把所有的情况都考虑进去，有时候不小心就会忘掉
当前字符为‘0’的情况
*/
class Solution {
public:
    int numDecodings(string s) {
    	if (s.empty() || s == "0")
    		return 0;

        int pre = 0, cur = 1;
        
        for (int i = 1; i <= s.size(); i++) {
        	if (s[i - 1] == '0')
        		cur = 0;

        	if (!(s[i - 2] == '1' || (s[i - 2] == '2' && s[i - 1] <= '6')))
        		pre = 0;

      		int tmp = cur;
      		cur = pre + cur;
      		pre = tmp;
        }
        return cur;
    }
};
```
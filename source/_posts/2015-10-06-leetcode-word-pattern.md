---
layout: post
title:  "LeetCode: Word Pattern"
categories: LeetCode
date:   2015-10-06 21:40:20
tags: ["Hash Table"]
---

> Given a pattern and a string str, find if str follows the same pattern.

> Examples:
> + pattern = "abba", str = "dog cat cat dog" should return true.
> + pattern = "abba", str = "dog cat cat fish" should return false.
> + pattern = "aaaa", str = "dog cat cat dog" should return false.
> + pattern = "abba", str = "dog dog dog dog" should return false.
> 
> Notes:
> + Both pattern and str contains only lowercase alphabetical letters.
> + Both pattern and str do not have leading or trailing spaces.
> + Each word in str is separated by a single space.
> + Each letter in pattern must map to a word with length that is at least 1.

思路还是很简单的，使用如`'a'->'dog', 'b'->'cat'`这样的形式记录就可以，但是在做题时只是考虑到了单向的不能重复，而没有考虑到`'dog'->'a', 'cat'->'b'`这样的也不能重复，亦即`'abba'->'dog dog dog dog'`这种形式的亦是错误的，以后还是要思虑周全一点才是。

这里不想自己用c++实现string的split方法，就用java来解题了：
``` java
public class Solution {
    public boolean wordPattern(String pattern, String str) {
        String[] strs = str.split(" ");
        if (strs.length != pattern.length())
            return false;

        Map<String, String> hash = new HashMap<String, String>();
        Map<String, String> set = new HashMap<String, String>();
        for (int i = 0; i < strs.length; i++) {
            String tmp = pattern.substring(i, i + 1);
            if (hash.get(tmp) != null) {
                if (!hash.get(tmp).equals(strs[i]))
                    return false;
            } else {
                if (set.get(strs[i]) == null) {
                     hash.put(tmp, strs[i]);
                     set.put(strs[i], tmp);
                } else 
                    return false;
            }
        }
        return true;
    }
}
```


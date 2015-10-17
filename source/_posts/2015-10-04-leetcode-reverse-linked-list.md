---
layout: post
title:  "LeetCode: Reverse Linked List"
categories: LeetCode
date:   2015-10-04 18:50:20
tags: ["Linked List"]
---

> Reverse a singly linked list.

思路还是很简单的，有三个节点的临时变量就可以了，主要是代码要写得精炼。

代码如下：

``` cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head) return NULL;
        ListNode *cur = head, *pre = NULL;
        while (cur) {
            ListNode *tmp = cur->next;
            cur->next =  pre;
            pre = cur;
            cur = tmp;
        }
        return pre;
    }
};
```


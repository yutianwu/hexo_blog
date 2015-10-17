---
layout: post
title:  "LeetCode: Remove Linked List Elements"
categories: LeetCode
date:   2015-10-08 13:42:20
tags: ["Linked List"]
---

> Remove all elements from a linked list of integers that have value val.

> Example
> 
> Given: 1 --> 2 --> 6 --> 3 --> 4 --> 5 --> 6, val = 6
> 
> Return: 1 --> 2 --> 3 --> 4 --> 5

思路很简单，但是注意移动指针的条件：只有当没有删除节点时，指针才往后移，以后切记要仔细一点。

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
    ListNode* removeElements(ListNode* head, int val) {
        ListNode *dummy = new ListNode(0);
        dummy->next = head;
        ListNode *ptr = dummy;
        while (ptr != NULL && ptr->next != NULL) {
            if (ptr->next->val == val) {
                if (ptr->next->next == NULL) 
                    ptr->next = NULL;
                else {
                    ptr->next->val = ptr->next->next->val;
                    ptr->next->next = ptr->next->next->next;
                }
            } else {
                ptr = ptr->next;
            }
        }
        return dummy->next;
    }
};```
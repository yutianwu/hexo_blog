---
layout: post
title:  "堆排序: Merge k Sorted Lists"
date:   2014-10-23 17:10:20
categories: problems
---

Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

这个题如果按照常规思路来做得话，就是将k个链表两两合并，但是这样做的时间复杂度高.

所以我们可以找时间复杂度更小一点的思路：

我们如果把k个链表一起合并，那么我们第一个加入链表的就是这k个链表首节点中最小的。并且每次都是这样。这种思路和
优先级队列是一样的，每次取出最小值。因此我们可以使用优先级队列来做。

``` cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
 
class MinHeap
{
public:
    bool operator() (const ListNode *lhs, const ListNode *rhs) const
    {
        return (lhs->val > rhs->val);
    }
};

class Solution {
public:
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        if (lists.size() == 0) return NULL;
        
        //使用每一个链表的头结点构建一个最小堆
        priority_queue<ListNode *, vector<ListNode *>, MinHeap> que;
        for (int i = 0; i < lists.size(); i++) {
            if (lists[i] != NULL) 
                que.push(lists[i]);
        }
        ListNode dummy(-1);
        ListNode *tail = &dummy;
        
        //如果队列不为空
        while(!que.empty()) {
            ListNode *top = que.top();
            que.pop();
            
            //链接最小节点
            tail->next = top;
            tail = top;
            
            //如果出列的节点还有下一个节点，那么再将下一个节点入列，构建堆
            if (top->next != NULL) {
                que.push(top->next);
            }
        }
        
        return dummy.next;
    }
};
```

---
layout: post
title:  "LeetCode: Implement Queue using Stacks"
categories: LeetCode
date:   2015-10-04 19:17:20
tags: ["Stack"]
---

> Implement the following operations of a queue using stacks.

> + push(x) -- Push element x to the back of queue.
> + pop() -- Removes the element from in front of queue.
> + peek() -- Get the front element.
> + empty() -- Return whether the queue is empty.

主要是push的时候对整个栈进行了一个翻转，代码如下：

``` cpp
class Queue {
private:
    stack<int> s;
public:
    // Push element x to the back of queue.
    void push(int x) {
        stack<int> reverse;
        while (!s.empty()) {
            reverse.push(s.top());
            s.pop();
        }
        
        stack<int> tmp;
        tmp.push(x);
        while (!reverse.empty()) {
            tmp.push(reverse.top());
            reverse.pop();
        }
        s = tmp;
    }

    // Removes the element from in front of queue.
    void pop(void) {
        if (!s.empty()) 
            s.pop();
    }

    // Get the front element.
    int peek(void) {
        return s.top();
    }

    // Return whether the queue is empty.
    bool empty(void) {
        return s.empty();
    }
};```


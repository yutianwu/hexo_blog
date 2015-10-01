---
layout: post
title:  "二分搜索: Insert Interval"
date:   2014-11-20 17:40:20
categories: problems
---

<a href="https://oj.leetcode.com/problems/insert-interval/">Insert Interval</a>

Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their start times.

Example 1:

Given intervals $[1,3],[6,9]$, insert and merge $[2,5]$ in as $[1,5],[6,9]$.

Example 2:

Given $[1,2],[3,5],[6,7],[8,10],[12,16]$, insert and merge $[4,9]$ in as $[1,2],[3,10],[12,16]$.

This is because the new interval $[4,9]$ overlaps with $[3,5],[6,7],[8,10]$.

###分析:

这个题算是一个很典型的二分搜索的题，但是首先我们要明确到底我们需要查找的什么。

比如举个最简单的例子：

我们要在$[4,5],[8,9],[12,13]$里插入一个$[6,7]$，那么我们需要找的是在intervals中interval.end小于newInerval.start的最大值，也就是第一个；同时我们还需要找出在intervals中interval.start大于newInterval.end的最小值，也就是第二个。所以我们就知道我们要插入的地方就是第一个和第二个之间。但是在这里，我们又需要注意还有两个小问题，就是newInterval.start可能要大于后一个的end，而newInterval.end可能又会小于前一个的start。

还有一个需要注意的就是在这个题中所用的二分搜索的方法，由于我们这里不精确求一个等于某个数值的位置，所以就不能像以前的二分查找一样。我们这里查找的是一个半闭半开的区间。

如果熟悉了这里的两个查找，那么二分查找基本上算了解了。

``` cpp
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    vector<Interval> insert(vector<Interval> &intervals, Interval newInterval) {
        vector<Interval> result;
        if (intervals.size() == 0) {
            result.push_back(newInterval);
            return result;
        }
        int left = searchRight(intervals, newInterval.start);   //找出小于start的最大值
        int right = searchLeft(intervals, newInterval.end);    //找出大于end的最小值
        
        int start = newInterval.start;
        int end = newInterval.end;

        int tmp_start = start, tmp_end = end;
        if ((left + 1) < intervals.size() && start >= intervals[left + 1].start) 
            tmp_start = intervals[left + 1].start;
        if ((right - 1) >= 0 && end <= intervals[right - 1].end) 
            tmp_end = intervals[right - 1].end;
        Interval tmp(tmp_start, tmp_end);
        result.push_back(tmp);
        
        for (int i = 0; i <= left; i++)
            result.push_back(intervals[i]);

        for (int i = right; i < intervals.size(); i++) 
            result.push_back(intervals[i]);
        
        return result;
    }
    
    //找出intervals中interval.start大于val的最小值
    int searchLeft(vector<Interval> &intervals, int val) {
        int start = -1, end = intervals.size();
        while (end - start > 1) {
            int mid = start + (end - start)/2;
            if (intervals[mid].start > val) {
            	//如果mid满足条件，那么解的存在范围为(start, mid]
                end = mid;
            } else {
            	//如果mid不满足条件，那么解的存在范围为(mid, end]
                start = mid;
            }
        }
        return end;
    }
    
    //找出intervals中interval.end小于val的最大值
    int searchRight(vector<Interval> &intervals, int val) {
        int start = -1, end = intervals.size();
        while (end - start > 1) {
            int mid = start + (end - start)/2;
            if (intervals[mid].end < val) {
            	//如果mid满足条件，那么解的存在范围为[mid, end)
                start = mid;
            } else {
            	//如果mid不满足条件，那么解的存在范围为[start, mid)
                end = mid;
            }
        }
        return start;
    }
};
```
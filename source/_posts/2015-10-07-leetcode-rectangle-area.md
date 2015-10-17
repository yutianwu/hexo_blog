---
layout: post
title:  "LeetCode: Rectangle Area"
categories: LeetCode
date:   2015-10-07 22:11:20
tags: ["Math"]
---

> Find the total area covered by two rectilinear rectangles in a 2D plane.

> Each rectangle is defined by its bottom left corner and top right corner as shown in the figure.

想法很简单，就是用两个矩形的总面积减去相交部分的面积就可以了。

所以只要求出相交部分面积就可以了。先求相交部分矩形的长度，假设两个矩形的两个顶点分别为$(A, B), (C, D)$与$(E, F), (G, H)$,如果两个矩形相交，那么相交矩形的长度应该为$min(G, C) - max(A, E)$，而如果两个矩形不相交，那么求出来的结果应该为负；同理，可以求出相交矩形的宽度。

``` cpp
class Solution {
public:
    int computeArea(int A, int B, int C, int D, int E, int F, int G, int H) {
        long long x = 0, y = 0;
        x = (long long)(G <= C ? G : C) - (A >= E ? A : E);
        y = (long long)(D <= H ? D : H) - (B >= F ? B : F);
        x = x >= 0 ? x : 0;
        y = y >= 0 ? y : 0;
        return (C - A) * (D - B) + (G - E) * (H - F) - x * y;
    }
};
```


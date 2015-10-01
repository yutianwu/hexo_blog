---
layout: post
title:  "图:并查集"
date:   2014-10-26 14:40:20
categories:   algorithms
---

并查集是一种树型的数据结构，其保持着用于处理一些不相交集合（Disjoint Sets）的合并及查询问题。

并查集一般支持两种操作：

1. 查找	查询两个元素a和b是否在同一个集合
2. 合并	合并a和b所在的两个集合

并查集一般使用树形结构，但不是二叉树。下面具体说说并查集的几个操作：

1. 初始化

首先准备的n个元素都是没有边的。

![ufind1](/img/union_find_1.png)

2. 合并

合并的时候就是将一个集合的根指向另一个集合的根。如下图：

![ufind1](/img/union_find_2.png)

但是在合并的时候一般会对合并的操作进行优化，我们会将树低的集合的根指向树高的集合的根，所以我们会顶一个rank的量来记录树高。

3. 查询

查询就是往上查找，看两个元素的根节点是否相同。

![ufind1](/img/union_find_3.png)

在查找的时候，同样可以对并查集进行优化，对并查集的路径进行压缩。我们在查找的过程中把每个节点都指向它的根节点。

![ufind1](/img/union_find_4.png)

代码如下:

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>

using namespace std;

const int MAX_N = 50000;    //最大个数
int par[MAX_N];             //节点的父节点
int height[MAX_N];          //树的高度
int N;                      //个数

//初始化并查集
void init(int n) {
    for (int i = 0; i < n; i++) {
        par[i] = i;
        height[i] = 0;
    }
}

//找出x所在树的根节点
int find(int x) {
    if (par[x] == x) {
        return x;
    } else {
        return par[x] = find(par[x]); //把父节点直接指向根节点
    }
}

//合并x,y所属的集合
void unite(int x, int y) {
    x = find(x);
    y = find(y);
    
    if (x == y)
        return;
    
    //如果x所在集合树的高度小于y所在集合树的高度，那么将
    //x的父节点指向y，反之亦然
    //如果x，y所在集合的树的高度相等，那么随便怎么样都行，
    //但是将最后指向的树的高度加一
    if (height[x] < height[y]) {
        par[x] = y;
    } else {
        par[y] = x;
        if (height[x] == height[y])
            height[x]++;
    }
}

int main() {

}
```
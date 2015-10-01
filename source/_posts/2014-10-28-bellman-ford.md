---
layout: post
title:  "图:单源点最短路径之Bellman-Ford算法"
date:   2014-10-28 17:48:20
categories:   algorithms
---

Bellman-Ford算法能够在一般的情况下(存在负权边的情况)， 解决单源点最短路径问题，而且能够判断图中是否存在负值回路。

算法用到了两个辅助数组D,P。使用D来记录源点到各个点得最短距离。使用P来记录最短路的路径。

算法的思路如下:

1. 对两个数组进行初始化，将D[0]赋值为0，表示是以0号顶点为源点。
2. 对图中的每一条边进行V-1次松弛操作，得到源点到每个顶点的最短路径。
3. 如果还能够对最短路径进行优化，表示图中存在负值回路。

代码如下：
``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>

using namespace std;

//节点
struct Node {
    int val;
    int to;
    Node(int to, int val):to(to), val(val) {}
};

const int INF = 1000000000;

int V;                      //顶点数
vector<vector<Node> > G;    //图
vector<int> D;              //距离
vector<int> P;              //路径

//松弛操作
//对一条边u-->v,权值为val，如果
//d[v] > d[u] + val，那么可以对这条边进行松弛操作，即更新源点到v的最短路径
void relax(int u, int v, int val) {
    if (D.size() > u && P.size() > v && D[v] > D[u] + val) {
        D[v] = D[u] + val;
        P[v] = u;
    }
}

bool bellman_ford() {
    //对每条边进行松弛，松弛V-1次
    //为什么要松弛V-1次呢，因为V个点中路径最长为V-1，所以每次松弛都能够得到最短路径中的一条边，
    //松弛V-1次以后，就得到了源点到所有点得最短的路径
    for (int i = 0; i < V; i++) {
        for (int m = 0; m < G.size(); m++) {
            for (int n = 0; n < G[m].size(); n++) {
                relax(m, G[m][n].to, G[m][n].val);
            }
        }
    }
    
    //如果存在负值回路，那么还可以松弛，返回false
    for (int m = 0; m < G.size(); m++) {
        for (int n = 0; n < G[m].size(); n++) {
            if (D[G[m][n].to] > D[m] + G[m][n].val)
                return false;
        }
    }
    //如果没有负值回路，返回true
    return true;
}

int main() {
    /* example input
     4
     0 1 x x
     x 0 2 x
     x x 0 3
     x x x 0
     
     5
     0 1  x x x
     x 0 -5 x x
     x x  0 2 3
     x x  x 0 x
     x 1  x x 0
     */
    cin>>V;
    G.resize(V);
    D.resize(V);
    P.resize(V);
    
    fill(D.begin(), D.end(), INF);
    D[0] = 0;
    fill(P.begin(), D.end(), V);
    
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            char w;
            cin>>w;
            if (i != j && w != 'x') {
                G[i].push_back(Node(j, w - '0'));
            }
        }
    }
    cout<<bellman_ford()<<endl;
    
    for (int i = 0; i < D.size(); i++) {
        cout<<D[i]<<endl;
    }
}
```
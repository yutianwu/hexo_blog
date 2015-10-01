---
layout: post
title:  "图: 二分图(用邻接表实现)"
date:   2014-10-14 20:26:20
categories: problems
---

给定一个具有n个顶点的图。要给图上每个顶点染色，并且要使相邻的顶点颜色不同。问是否能最多使用2种颜色进行染色？题目保证没有重边和自环。

这个题可以使用dfs来实现，思路还是挺简单的。

假设有一个简单图，随意从一个顶点开始染色，然后给相邻的顶点染上相反的色，如果相邻顶点和自己的色相同，那么说明不是二分图。

但是如果是一个森林的话，那么我们还要对每个顶点进行判断，判断是否已经染色了，毕竟就算前一个图是二分图，也只能把自己所有的顶点染色。

这是第一个用c++来实现的题，在这里主要是学习用邻接表来实现图，数组的话占用空间太多了。

``` cpp
#include <iostream>
#include <vector>

using namespace std;

const int MAX_V = 10000;

vector<int> G[MAX_V]; //图
int N;  //边数
int V;  //顶点数
int color[MAX_V];

bool dfs(int v, int c) {
    color[v] = c;
    for (int i = 0; i < G[v].size(); i++) {
        if (color[G[v][i]] == c)
            return false;
        
        if (color[G[v][i]] == 0 && !dfs(G[v][i], -c))
            return false;
    }
    return true;
}

void solve() {
    for (int i = 0; i < V; i++) {
        if (color[i] == 0) {
            if (!dfs(i, 1)) {
                cout<<"No"<<endl;
                return;
            }
        }
    }
    cout<<"Yes"<<endl;
}

int main()
{
    cin>>V;
    cin>>N;
    for (int i = 0; i < N; i++) {
        int a, b;
        cin>>a>>b;
        G[a].push_back(b);
        G[b].push_back(a);
    }
    fill(color, color + V, 0);
    solve();
    return 0;
}
```
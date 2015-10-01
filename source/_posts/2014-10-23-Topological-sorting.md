---
layout: post
title:  "图：拓扑排序(Topological sort)"
date:   2014-10-23 21:16:20
categories:   algorithms
tags: [图]
---

由一个有向无环图的顶点组成的序列，当且仅当满足下列条件时，称为该图的一个拓扑排序。

+ 每个顶点出现且只出现一次；
+ 若A在序列中排在B的前面，则在图中不存在从B到A的路径。

也可以定义为：拓扑排序是对有向无环图的顶点的一种排序，它使得如果存在一条从顶点A到顶点B的路径，那么在排序中B出现在A的后面。

拓扑排序一般用在工作流中，即跟顺序有关的事情中。比如我要穿衣服，那么我得先穿袜子，再穿裤子，最后穿鞋子。拓扑排序就是用来解决这类问题的。

拓扑排序一般使用DFS来实现。思路如下：


TOPOLOGICAL-SORT(G)

1. call DFS(G) to visit the G
2. as each vetex is finished, pushi it into the stack
3. return 

由上可以看出，每次当遍历完一个节点的所有子节点时，将该节点压栈，到最后就会得到一个拓扑序。

为什么呢，我们假设有一个最简单的图：

```
	1->2->3->4
```

那么遍历完每一个节点的所有子节点的顺序应该是4，3，2，1.即我们先遍历完4的所有子节点，最后才遍历完1的所有子节点。这也是我们为什么要用栈而不用队列的原因。

代码如下：

``` cpp
#include <iostream>
#include <queue>
#include <vector>
#include <stack>
using namespace std;

const int MAX_V = 1000;      //最大的顶点数

vector<int> G[MAX_V];   //图的邻接表形式
int V;                  //顶点数
int E;                  //边数
int status[MAX_V];      //顶点的状态数组
stack<int> topology;

void dfs(int s) {
    status[s] = 1;      //顶点s已经访问过
    for (int v = 0; v < G[s].size(); v ++) {
        int to = G[s][v];
        if (status[to] == 0)
            dfs(to);
    }
    topology.push(s);   //当访问完所有子节点时，将节点入栈
}

int main() {
    cin>>V;
    cin>>E;
    for (int i = 0; i < E; i++) {
        int a, b;
        cin>>a>>b;
        G[a - 1].push_back(b - 1);
    }
    
    fill(status, status + V, 0);
    for (int i = 0; i < V; i++) {
        if (status[i] == 0)
            dfs(i);
    }
    
    while (!topology.empty()) {
        cout<<topology.top() + 1<<endl;
        topology.pop();
    }
}
```
---
layout: post
title:  "最大流: Edmonds-Karp算法"
date:   2014-11-20 15:03:20
categories:   algorithms
---


Edmonds-Karp算法是Ford-Fulkerson算法的一种实现，时间复杂度为$O(VE^2)$.

思想就是每次在源点和终点之间都找出一条增广路径，然后根据这条增广路径的容量计算图的残留网络，继续在残留网络上找增广路径，直至找不出增广路径为止，最后得到的增广路径的和即为图的最大流。


![trie1](/img/edmonds_karp_1.png)

代码实现入下：

``` cpp

#include <iostream>
#include <vector>
#include <queue>
#include <math.h>

using namespace std;

const int MAX_NODE = 100; //最大顶点数

int capacities[MAX_NODE][MAX_NODE]; //网络容量
int flow[MAX_NODE][MAX_NODE];       //网络流
int parent[MAX_NODE];               //路径
int pathCapacity[MAX_NODE];         //路径上的容量
vector<int> graph[MAX_NODE];        //图的邻接表示

int bfs(int start, int sink){
    //初始化路径数组和路径容量数组
    fill(parent, parent + MAX_NODE, -1);
    fill(pathCapacity, pathCapacity + MAX_NODE, 0);
    
    queue<int> q;
    q.push(start);
    
    pathCapacity[start] = INT_MAX;  //设置源点容量
    
    while (!q.empty()) {
        int current = q.front();
        q.pop();
        
        for (int i = 0; i < graph[current].size(); i++) {
            int to = graph[current][i];
            
            //如果邻接节点还没有被访问过且残留路径大于零
            if (parent[to] == - 1
                && capacities[current][to] - flow[current][to] > 0) {
                
                parent[to] = current;
                pathCapacity[to] = min(pathCapacity[current],
                                       capacities[current][to] - flow[current][to]);
                
                //如果已经到达终点，那么已经找到了一条路径，返回路径的容量
                if (to == sink)
                    return pathCapacity[to];
                //否则将当前访问节点加入队列
                q.push(to);
            }
        }
    }
    return 0;
}

int edmondsKarp(int start, int sink) {
    int maxFlow = 0;
    while (true) {
        //使用BFS算法先找出一条路径，并且得到路径的容量
        int cap = bfs(start, sink);
        //如果路径容量为零，则说明没有增广路径了，跳出循环
        if (cap == 0)
            break;
        
        //最大流加上路径的容量
        maxFlow += cap;
        
        //根据BFS得到的路径，计算网络流
        int current = sink;
        while (current != start) {
            int previous = parent[current];
            flow[previous][current] += cap;
            flow[current][previous] = -flow[previous][current];
            current = previous;
        }
    }
    return maxFlow;
}

int main() {
    
    /*
     example input:算导p405
     
     6 10
     0 3
     0 1 16
     1 2 12
     2 3 20
     1 5 10
     5 1 4
     2 5 9
     4 2 7
     0 5 13
     5 4 14
     4 3 4
     */
    int nodesCount, edgesCount;
    cin>>nodesCount>>edgesCount;
    int source, sink;
    cin>>source>>sink;
    
    for(int edge=0; edge<edgesCount; edge++)
    {
        int from, to, capacity;
        cin>>from>>to>>capacity;
        capacities[from][to]=capacity;
        graph[from].push_back(to);
    }
    int maxFlow = edmondsKarp(source, sink);
    cout<<maxFlow<<endl;
}
```
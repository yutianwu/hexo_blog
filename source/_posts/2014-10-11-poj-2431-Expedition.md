---
layout: post
title:  "poj: 2431-Expedition"
date:   2014-10-11 21:09:20
categories: problems
---


##Description

A group of cows grabbed a truck and ventured on an expedition deep into the jungle. Being rather poor drivers, the cows unfortunately managed to run over a rock and puncture the truck's fuel tank. The truck now leaks one unit of fuel every unit of distance it travels. 

To repair the truck, the cows need to drive to the nearest town (no more than 1,000,000 units distant) down a long, winding road. On this road, between the town and the current location of the truck, there are N (1 <= N <= 10,000) fuel stops where the cows can stop to acquire additional fuel (1..100 units at each stop). 

The jungle is a dangerous place for humans and is especially dangerous for cows. Therefore, the cows want to make the minimum possible number of stops for fuel on the way to the town. Fortunately, the capacity of the fuel tank on their truck is so large that there is effectively no limit to the amount of fuel it can hold. The truck is currently L units away from the town and has P units of fuel (1 <= P <= 1,000,000). 

Determine the minimum number of stops needed to reach the town, or if the cows cannot reach the town at all. 

##分析
这个题其实可以这样理解，车开过一个加油站我们可以把油记下，如果在路上油不够了随时可以加。

所以加油的策略就是我们只加油量最多的站。而这个策略我们可以使用优先级队列来实现。

在这个题中，有两个很好的地方需要注意：

1.c++中优先级队列的使用

2.c++中sort()函数的使用

如果c++的STL和标准类库中的工具用的好的话可以事半功倍。


``` cpp
#include <iostream>
#include <queue>

using namespace std;

const int MAX_N = 10000;

int n, L, P;
int A[MAX_N + 1], B[MAX_N + 1];

//定义加油站的结构体
struct node {
    int pos, fuel;
};

node positions[MAX_N + 1];

//自定义一个降序排序的比较函数
bool cmp(const node &a, const node &b) {
    return a.pos > b.pos;
}

void solve() {
    positions[n].pos = L; //终点位置
    positions[n].fuel = 0;
    n++;
    
    priority_queue<int> que;
    
    int ans = 0, pos = 0, tank = P;
    
    for (int i = 0; i < n; i++) {
        //要前进的距离
        int d = positions[i].pos - pos;
        
        //不断加油直到能够到下一个加油站
        while (tank < d) {
            if (que.empty()) {
                cout<<"-1"<<endl;
                return;
            }
            
            tank += que.top();
            que.pop();
            ans++;
        }
        
        tank -= d;
        pos = positions[i].pos;
        que.push(positions[i].fuel);
    }
    cout<<ans<<endl;
}

int main() {
    cin>>n;
    
    for (int i = n - 1; i >= 0; i--) {
        cin>>positions[i].pos>>positions[i].fuel;
    }
    
    sort(positions, positions + n, cmp); //对输入进行降序排序
    
    cin>>L>>P;
    
    for (int i = 0; i < n; i++) {
        positions[i].pos = L - positions[i].pos; //计算每一个加油站到起点的距离
    }
    
    solve();
}
```
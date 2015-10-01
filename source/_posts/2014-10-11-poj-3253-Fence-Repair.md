---
layout: post
title:  "poj: 3253-Fence Repair"
date:   2014-10-11 22:46
categories: problems
---

##Description

Farmer John wants to repair a small length of the fence around the pasture. He measures the fence and finds that he needs N (1 ≤ N ≤ 20,000) planks of wood, each having some integer length Li (1 ≤ Li ≤ 50,000) units. He then purchases a single long board just long enough to saw into the N planks (i.e., whose length is the sum of the lengths Li). FJ is ignoring the "kerf", the extra length lost to sawdust when a sawcut is made; you should ignore it, too.

FJ sadly realizes that he doesn't own a saw with which to cut the wood, so he mosies over to Farmer Don's Farm with this long board and politely asks if he may borrow a saw.

Farmer Don, a closet capitalist, doesn't lend FJ a saw but instead offers to charge Farmer John for each of the N-1 cuts in the plank. The charge to cut a piece of wood is exactly equal to its length. Cutting a plank of length 21 costs 21 cents.

Farmer Don then lets Farmer John decide the order and locations to cut the plank. Help Farmer John determine the minimum amount of money he can spend to create the N planks. FJ knows that he can cut the board in various different orders which will result in different charges since the resulting intermediate planks are of different lengths.


##代码实现
``` cpp

#include <iostream>
#include <queue>

using namespace std;

typedef long long ll;

int main(){
    int n;
    //这个题可以看看挑战程序设计竞赛一书47页的分析，这个题和霍夫曼编码很像
    //主要是stl中优先级队列的使用，事半功倍
    priority_queue<int, vector<int>, greater<int>> que; 
    ll ans = 0;
    cin>>n;
    for (int i = 0; i < n; i++) {
        int tmp = 0;
        cin>>tmp;
        que.push(tmp);
    }
    
    while (que.size() > 1) {
        int l1, l2;
        l1 = que.top();
        que.pop();
        l2 = que.top();
        que.pop();
        
        ans += l1 + l2;
        que.push(l1 + l2);
    }
    
    cout<<ans<<endl;
    return 0;
}
```
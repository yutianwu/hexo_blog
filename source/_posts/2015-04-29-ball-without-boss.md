---
layout: post
title:  "动态规划: 树形dp-没有上司的舞会"
date:   2015-04-29 21:16:20
categories: problems
tags: [动态规划]
---

###题目描述

>Ural大学有N个职员，编号为1~N。他们有从属关系，也就是说他们的关系就像一棵以校长为根的树，父结点就是子结点的直接上司。每个职员有一个快乐指数。现在有个周年庆宴会，要求与会职员的快乐指数最大。但是，没有职员愿和直接上司一起与会。意思是如果某人直接上司去了，那么他就不去。

###输入描述
>第一行一个整数N。(1<=N<=6000)
>接下来N行，第i+1行表示i号职员的快乐指数Ri。(-128<=Ri<=127)
>接下来N-1行，每行输入一对整数L,K。表示K是L的直接上司。
>最后一行输入0,0。

###输出描述
>输出最大的快乐指数。

###样例输入
>7
>1
>1
>1
>1
>1
>1
>1
>1 3
>2 3
>6 4
>7 4
>4 5
>3 5
>0 0

###样例输出
>5

###思路

设状态f[k][0],表示k不参加舞会时最大的快乐指数，f[k][1]表示k参加舞会时的最大快乐指数
状态转移方程如下：

$f[k][0]=\sum max \left( f[l][0], f[l][1] \right) $

$f[k][1]=\sum f[l][0] + happy[k] $


树形dp的以大特点就是数据是分好几块的，相互之间并不影响，比如说处理左子树和右子树时，两边的数据是不会相互影响的，即只用到了一部分数据。而一般的dp算法都是从0-n一步一步算出来的。

``` cpp
#include <iostream>
#include <vector>
using namespace std;

const int MAX_N = 6000;

int n;
vector<int> G[MAX_N + 1];
int happy[MAX_N + 1];
int f[MAX_N + 1][2];
int has_boss[MAX_N + 1];


void dp(int k) {
    f[k][1] = happy[k];
    for (int i = 0; i < G[k].size(); i++) {
        int l = G[k][i];
        dp(l);
        f[k][1] += f[l][0];
        f[k][0] += max(f[l][0], f[l][1]);
    }
}

void solve() {
    int i = 0;
    for (i = 1; i <= n; i++) {
        if (!has_boss[i])
            break;
    }
    dp(i);
    cout<<max(f[i][0], f[i][1])<<endl;
}

int main() {
    cin>>n;
    for (int i = 1; i <= n; i++) {
        cin>>happy[i];
    }
    
    int l, k;
    for (int i = 1; i <= n - 1; i++) {
        cin>>l>>k;
        G[k].push_back(l);
        has_boss[l] = 1;
    }
    
    cin>>l>>k;
    
    solve();
}
```
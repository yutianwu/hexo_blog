---
layout: post
title:  "动态规划: 树形dp-访问艺术馆"
date:   2015-04-27 21:58:20
categories: problems
tags: [动态规划]
---

###题目描述

>皮尔是一个出了名的盗画者，他经过数月的精心准备，打算到艺术馆盗画。艺术馆的结构，每条走廊要么分叉为二条走廊，要么通向一个展览室。皮尔知道每个展室里藏画的数量，并且他精确地测量了通过每条走廊的时间，由于经验老道，他拿下一副画需要5秒的时间。你的任务是设计一个程序，计算在警察赶来之前(警察到达时皮尔回到了入口也算)，他最多能偷到多少幅画。
![trie1](/img/visit_gallary.jpg)

###输入描述
>第1行是警察赶到得时间，以s为单位。第2行描述了艺术馆得结构，是一串非负整数，成对地出现：每一对得第一个数是走过一条走廊得时间，第2个数是它末端得藏画数量；如果第2个数是0，那么说明这条走廊分叉为两条另外得走廊。数据按照深度优先得次序给出，请看样例

###输出描述
>输出偷到得画得数量

###样例输入
>60
>7 0 8 0 3 1 14 2 10 0 12 4 6 2

###样例输出
>2

``` cpp
#include <iostream>
#include <vector>
using namespace std;

const int MAX_N = 1010;

int LIMIT, T[MAX_N], P[MAX_N];

int n, L[MAX_N], R[MAX_N];

int dp[MAX_N][MAX_N];

int build_tree() {
    n++;
    cin>>T[n]>>P[n];
    
    T[n] *= 2; //进去还得出来，所以要两倍的时间
    int now = n;
    if (P[now] == 0) {
        L[now] = build_tree();
        R[now] = build_tree();
    }
    return now;
}

int solve(int root, int time) {
    if (dp[root][time] != -1)
        return dp[root][time];
    if (time <= 0)
        return dp[root][time] = 0;
    
    int ct = time - T[root];
    if (!L[root] && !R[root]) {
        if (P[root] * 5 <= ct)
            return dp[root][time] = P[root];
        else
            return dp[root][time] = ct / 5;
    }
    
    dp[root][time] = 0;
    for (int t = 0; t <= ct; t++) {
        int lp = solve(L[root], ct - t);
        int rp = solve(R[root], t);
        dp[root][time] = max(dp[root][time], lp + rp);
    }
    
    return dp[root][time];
}

int main() {
    n = 0;
    cin>>LIMIT;
    build_tree();
    memset(dp, -1, sizeof(dp));
    solve(1, LIMIT);
    cout<<dp[1][LIMIT]<<endl;
}
```
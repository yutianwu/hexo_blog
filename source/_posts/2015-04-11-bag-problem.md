---
layout: post
title:  "动态规划：背包问题"
date:   2015-4-11 23:21:20
categories:   problems
tags: [动态规划]
---

###1.01背包问题

>有n个重量和价值分别为wi和vi的物品。从这些物品中挑选出总重量不超过W的物品，求所有
挑选方案中价值总和的最大值。
>其中`1 < n < 100`, `1 < wi, vi < 100`, `1 < W < 10000`

在前面的博客中说过01背包可以用DFS来解，但是使用DFS来解得话时间复杂度很高，所以可以使用动态规划
来解这道题。

首先，可以得到递推关系式：

令dp[i + 1][j]表示从前i个物品中选出总重量不超过j的物品时总价值的最大值，那么：

```
    dp[0][j] = 0;

                    dp[i][j]    j < w[i]
    dp[i+1][j] = 
                    max{dp[i][j], dp[j-w[i]] + vi}
```

代码如下：
``` cpp
/*
 此题见于挑战一书51页
 */
#include <iostream>
using namespace std;

const int MAX_N = 100;

int n, W;
int w[MAX_N], v[MAX_N];
int dp[MAX_N + 1][MAX_N + 1];

void solve() {
    for (int i = 0; i < n; i++) {
        for (int j = 0; j <= W; j++) {
            if (j < w[i]) {
                dp[i+1][j] = dp[i][j];
            } else {
                dp[i+1][j] = max(dp[i][j], dp[i][j-w[i]] + v[i]);
            }
        }
    }
    cout<<dp[n][W]<<endl;
}

int main() {
    cin>>n;
    for (int i = 0; i < n; i++) {
        cin>>w[i]>>v[i];
    }
    cin>>W;
    memset(dp, 0, sizeof(dp));
    solve();
} 
```

###2.完全背包问题
>有n种重量和价值分别为wi和vi的物品。从这些物品中挑选出总重量不超过W的物品，求所有
挑选方案中价值总和的最大值。这里每种物品可以挑选任意多件。
>其中`1 < n < 100`, `1 < wi, vi < 100`, `1 < W < 10000`

这个问题和上一个问题的区别在于每种物品都可以挑选任意多件。但是递推关系和上一个问题差不多。

令dp[i + 1][j]表示从前i个物品中选出总重量不超过j的物品时总价值的最大值，那么：
```
    dp[0][j] = 0;
               
    dp[i+1][j] = max{dp[i][j - k*w[i]] + k*v[i] | k >= 0}
```

代码如下：

``` cpp
/*
 此题见于挑战一书51页
 */
#include <iostream>
using namespace std;

const int MAX_N = 100;

int n, W;
int w[MAX_N], v[MAX_N];
int dp[MAX_N + 1][MAX_N + 1];

void solve() {
    for (int i = 0; i < n; i ++) {
        for (int j = 0; j <= W; j++) {
            for (int k = 0; k*w[i] <= j; k++) {
                dp[i+1][j] = max(dp[i+1][j], dp[i][j - k*w[i]] + k*v[i]);
            }
        }
    }
    
    cout<<dp[n][W];
}

int main() {
    cin>>n;
    for (int i = 0; i < n; i++) {
        cin>>w[i]>>v[i];
    }
    cin>>W;
    memset(dp, 0, sizeof(dp));
    solve();
}
```

但是上面这种解法的时间复杂度可能达到O(nW^2),所以可以继续对题进行优化，在dp[i+1][j]的计算中选择k(k>=1)个i物品的情况与在dp[j+1][j-w[i]]的计算中选择k-1个i物品的情况
是一样的，所以dp[i+1][j]的递推中k>=1的部分计算已经在dp[i+1][j-w[i]]的计算中完成
了。所以可以按照如下方式变形：
```
	dp[i+1][j] = max{dp[i][j - k*wi] - k*vi | k >= 0}
		   	   = max{dp[i][j], max{ dp[i][j - k*wi] + k*vi | k >= 1} }
		   	   = max{dp[i][j], max{dp[i][(j - wi) - k*wi] + k*vi | k >= 0} + vi}
		   	   = max{dp[i][j], dp[i+1][j-wi] + vi}
```

代码如下：

``` cpp
/*
 此题见于挑战一书51页
 */
#include <iostream>
using namespace std;

const int MAX_N = 100;

int n, W;
int w[MAX_N], v[MAX_N];
int dp[MAX_N + 1][MAX_N + 1];

void solve() {
    for (int i = 0; i < n; i ++) {
        for (int j = 0; j <= W; j++) {
            if (j < w[i]) {
                dp[i + 1][j] = dp[i][j];
            } else {
                dp[i + 1][j] = max(dp[i][j], dp[i + 1][j - w[i]] + v[i]);
            }
        }
    }
    
    cout<<dp[n][W];
}

int main() {
    cin>>n;
    for (int i = 0; i < n; i++) {
        cin>>w[i]>>v[i];
    }
    cin>>W;
    memset(dp, 0, sizeof(dp));
    solve();
}
```

###3.使用滚动数组来减少空间复杂度
对这种一行一行计算结果的情况，我们有时可以用重复利用一个滚动数组来降低空间复杂度。

``` cpp
int dp[MAX_W + 1]

/*
01背包问题
*/

void solve() {
	for (int i = 0; i < n; i++) {
		for (int j = W; j >= w[i]; j--) {
			dp[j] = max(dp[j], dp[i - w[i]] + v[i]);
		}
	}
	cout<<dp[W]<<endl;
}

/*
完全背包问题
*/

void solve() {
	for (int i = 0; i < n; i++) {
		for (int j = w[i]; j <= W; j++) {
			dp[j] = max(dp[j], dp[j - w[i]] + v[i]);
		}
	}
	cout<<dp[W]<<endl;
}
```
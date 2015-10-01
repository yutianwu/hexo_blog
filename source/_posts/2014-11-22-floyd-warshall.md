---
layout: post
title:  "图：Floyd-Warshall算法"
date:   2014-11-22 11:40:20
categories:   algorithms
---

Floyd-Warshall算法是一种动态规划算法，动规算法的话只要得出递推公式，那么程序就会非常简洁。

这里也不给出算法的递推公式，详见算法导论第25章。

在这里，需要强调的一点就是，动规算法一般是需要辅助的数组来存储中间值的，但是有时我们可以巧妙的利用算法的执行顺序，使用滚动数组就可以避免使用辅助数组。

在这个算法中，我们也并没有使用辅助数组，是因为我们在执行本轮计算的时候，并没有改变本轮所有计算所需要的前一轮的结果。所以我们并不需要一个辅助数组来存储上一轮的值。

``` cpp
#include <iostream>
using namespace std;

const int MAX_N = 100;
const int NIL = -1;
int G[MAX_N][MAX_N];
int P[MAX_N][MAX_N];
int n;

//算法主体
void floydWarshall() {
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                //根据递推公式来更新距离矩阵和路径矩阵
                if (G[i][j] > G[i][k] + G[k][j]) {
                    G[i][j] = min(G[i][j], G[i][k] + G[k][j]);
                    P[i][j] = P[k][j];
                }
            }
        }
    }
}

//输出两个顶点之间的路径
void printPath(int i, int j) {
    if (i == j)
        cout<<i;
    else if (P[i][j] == NIL)
        cout<<"there is no path from "<<i<<" to "<<j<<endl;
    else {
        printPath(i, P[i][j]);
        cout<<"->"<<j;
    }
}

int main() {
    /*
     example input:
     5
     0	3 	8	1000	-4
     1000	0	1000	1	7
     1000	4	0	1000	1000
     2	1000	-5	0	1000
     1000	1000	1000	6	0
     */
    cin>>n; //输入顶点个数
    //输入邻接矩阵，同时初始化前驱矩阵
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cin>>G[i][j];
            //初始化前驱矩阵
            if (i != j && G[i][j] != 1000)
                P[i][j] = i;
            else
                P[i][j] = NIL;
        }
    }
    
    floydWarshall();
    
    cout<<"result matrix:"<<endl;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cout<<G[i][j]<<"\t";
        }
        cout<<endl;
    }
    
    cout<<"path matrix:"<<endl;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cout<<P[i][j]<<"\t";
        }
        cout<<endl;
    }
    
    cout<<"paths:"<<endl;
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cout<<"\npath "<<i<<" to "<<j<<", length is"<<G[i][j]<<endl;
            printPath(i, j);
        }
    }
}
``` 

```
	5
	0	3 	8	1000	-4
	1000	0	1000	1	7
	1000	4	0	1000	1000
	2	1000	-5	0	1000
	1000	1000	1000	6	0
	result matrix:
	0	1	-3	2	-4	
	3	0	-4	1	-1	
	7	4	0	5	3	
	2	-1	-5	0	-2	
	8	5	1	6	0	
	path matrix:
	-1	2	3	4	0	
	3	-1	3	1	0	
	3	2	-1	1	0	
	3	2	3	-1	0	
	3	2	3	4	-1	
	paths:

	path 0 to 0, length is0
	0
	path 0 to 1, length is1
	0->4->3->2->1
	path 0 to 2, length is-3
	0->4->3->2
	path 0 to 3, length is2
	0->4->3
	path 0 to 4, length is-4
	0->4
	path 1 to 0, length is3
	1->3->0
	path 1 to 1, length is0
	1
	path 1 to 2, length is-4
	1->3->2
	path 1 to 3, length is1
	1->3
	path 1 to 4, length is-1
	1->3->0->4
	path 2 to 0, length is7
	2->1->3->0
	path 2 to 1, length is4
	2->1
	path 2 to 2, length is0
	2
	path 2 to 3, length is5
	2->1->3
	path 2 to 4, length is3
	2->1->3->0->4
	path 3 to 0, length is2
	3->0
	path 3 to 1, length is-1
	3->2->1
	path 3 to 2, length is-5
	3->2
	path 3 to 3, length is0
	3
	path 3 to 4, length is-2
	3->0->4
	path 4 to 0, length is8
	4->3->0
	path 4 to 1, length is5
	4->3->2->1
	path 4 to 2, length is1
	4->3->2
	path 4 to 3, length is6
	4->3
	path 4 to 4, length is0
	4
```
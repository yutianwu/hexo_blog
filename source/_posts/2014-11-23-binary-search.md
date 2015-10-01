---
layout: post
title:  "二分搜索"
date:   2014-11-23 11:38:20
categories:   algorithms
---

我们可以很快地写出在已排序数组中查找某一个数的代码，但是如果我们要找出某个数的上界和下届有时候就会多费点心思，在这里我们可以看看这三种方式的不同之处,另外，在实际应用中，我们可以不用自己是实现，直接使用STL中的upper_bound和lower_bound函数就可以了。

``` cpp
#include <iostream>
#include <vector>

using namespace std;

//精确查找某一个值
//如果有的话，返回第一个找到的值
//如果没有的话，返回-1
int binarySearch(vector<int> &A, int x) {
    int size = (int)A.size();
    int lb = 0, ub = size - 1;
    while (lb <= ub) {
        int mb = lb + (ub - lb)/2;
        if (A[mb] == x)
            return mb;
        else if (A[mb] > x)
            ub = mb - 1;
        else
            lb = mb + 1;
    }
    return -1;
}

//在数组中查找x的上界,即小于等于x的最大值
//如果有，返回上界
//如果没有，返回-1
int upperBound(vector<int> &A, int x) {
    int size = (int)A.size();
    int lb = -1, ub = size;
    while (ub - lb > 1) {
        int md = lb + (ub - lb) / 2;
        //如果满足条件，那么符合条件的范围就是[mid,ub)
        if (A[md] <= x)
            lb = md;
        else
            ub = md;
    }
    return lb;
}

//在数组中查找x的下界,即大于等于x的最小值
//如果有，返回下界
//如果没有，返回n
int lowerBound(vector<int> &A, int x) {
    int size = (int)A.size();
    int lb = -1, ub = size;
    while (ub - lb > 1) {
        int md = lb + (ub - lb) / 2;
        //如果满足条件，那么符合条件的范围就是(mid, ub]
        if (A[md] >= x)
            ub = md;
        else
            lb = md;
    }
    return ub;
}

//10 10 10 20 20 20 30 30
int main() {
    int myints[] = {10,20,30,30,20,10,10,20};
    vector<int> test(myints, myints + 8);
    sort(test.begin(), test.end());
    cout<<binarySearch(test, 15)<<endl;
    cout<<upperBound(test, 38)<<endl;
    cout<<lowerBound(test, 5)<<endl;
}
```
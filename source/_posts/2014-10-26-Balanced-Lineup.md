---
layout: post
title:  "线段树、平方分割:POJ 3264 Balanced Lineup"
date:   2014-10-26 15:56:20
categories: algorithms
---

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>

using namespace std;

const int MAX_HEIGHT = 2000000;
const int MIN_HEIGHT = 0;
const int MAX_N = 50000;
int N, Q;
int H[MAX_N + 1];
vector<pair<int, int> > bucket; //first存储最大值，second存储最小值

int main() {
    cin>>N>>Q;
    int bucket_size = sqrt((float)N);
    bucket.resize(bucket_size + 1);
    for (int i = 0; i <= bucket_size; i++) {
        bucket[i].first = MIN_HEIGHT;
        bucket[i].second = MAX_HEIGHT;
    }
    
    for (int i = 1; i <= N; i++) {
        cin>>H[i];
        bucket[i / bucket_size].first = max(bucket[i / bucket_size].first, H[i]);
        bucket[i / bucket_size].second = min(bucket[i / bucket_size].second, H[i]);
    }
    
    for (int i = 0; i < Q; i++) {
        int A, B;
        cin>>A>>B;
        if (A == B) {
            cout<<0<<endl;
            continue;
        }

        int daming = MIN_HEIGHT, xiaoming = MAX_HEIGHT;
        
        //如果A、B在一个桶
        while (A <= B && A % bucket_size != 0) {
            daming = max(H[A], daming);
            xiaoming = min(H[A], xiaoming);
            A++;
        }
        
        while (A <= B && (B + 1) % bucket_size != 0) {
            daming = max(H[B], daming);
            xiaoming = min(H[B], xiaoming);
            B--;
        }
        
        while (A <= B) {
            daming = max(bucket[A/bucket_size].first, daming);
            xiaoming = min(bucket[A/bucket_size].second, xiaoming);
            A += bucket_size;
        }
        
        cout<<(daming - xiaoming)<<endl;
    }
}
```
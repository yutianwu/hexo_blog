---
layout: post
title:  "位运算与深搜: subsets"
date:   2014-11-12 21:00:20
categories: problems
---

Given a set of distinct integers, S, return all possible subsets.

Note:
Elements in a subset must be in non-descending order.

The solution set must not contain duplicate subsets.

For example,

If S = [1,2,3], a solution is:

	[
	  [3],
	  [1],
	  [2],
	  [1,2,3],
	  [1,3],
	  [2,3],
	  [1,2],
	  []
	]

解法一:使用位运算

``` cpp
class Solution {
public:
    //使用位运算来实现
    //在这个题中数据量应该很小，S的大小应该小于32。。。
    vector<vector<int> > subsets(vector<int> &S) {
        vector<vector<int>> result;
        if (S.size() == 0) return result;
        
        int size = S.size();
        sort(S.begin(), S.end());
        
        int pos = 0;        
        //可以看出，时间复杂度是很高的，是2^size
        for (int i = 1; i <= pow(2, size); i++) {
            vector<int> row;
            for (int j = 0; j < size; j ++) {
                if ((pos >> j) & 1) row.push_back(S[j]); 
            }
            result.push_back(row);
            pos++;
        }
        return result;
    }
};
```

解法二: 还是是用位运算

``` cpp
class Solution {
public:
    //在上一个解法中，我们只是考虑到S.size() < 32的情况，
    //在这个解法中，S.size()可以大于32
    //在这个解法中，使用一个bits的数组来存储各个位的状态，比如我要存储42位
    //的状态信息，那么我只要获取bits[42/32]中42%32位的值就好了
    vector<vector<int> > subsets(vector<int> &S) {
        vector<vector<int>> result;
        if (S.size() == 0) return result;
        
        int size = S.size();
        sort(S.begin(), S.end());
        
        vector<int> bits(size/(8 * sizeof(int)) + 1, 0);
        while (!shouldStop(bits, size)) {
            vector<int> row;
            for (int j = 0; j < size; j ++) {
                if (getBit(bits, j)) row.push_back(S[j]); 
            }
            result.push_back(row);
            addOne(bits);
        }
        return result;
    }
    
    bool shouldStop(vector<int> &bits, int size) {
    	//如果前size位都为零，但是size+1位为一，那么久溢出了
        bool flow = false;
        for(int i = 0; i < size; i++) {
            flow = flow || getBit(bits, i);
        }
        return ~flow && getBit(bits, size); 
    }
    
    void addOne(vector<int> &bits) {
        bool flow = true;
        for (int i = 0; i < bits.size(); i++) {
            if (flow && bits[i] < pow(2, sizeof(int)*8) - 1) {
                bits[i]++;
                return;
            } 
            bits[i] = 0;
        }
    }
    
    bool getBit(vector<int> &bits, int pos) {
        int p_int = pos / (8 * sizeof(int));
        int p_bit = pos % (8 * sizeof(int));
        
        return (bits[p_int] >> p_bit) & 1;
    }
};
```

解法三: 深搜

``` cpp
class Solution {
public:
    vector<vector<int> > subsets(vector<int> &S) {
        vector<vector<int>> result;
        if (S.size() == 0) return result;
        sort(S.begin(), S.end());
        
        vector<int> row;
        dfs(result, row, S, 0);
        return result;
    }
    
    void dfs(vector<vector<int> > &result, vector<int> row, vector<int> &S, int pos) {
        if (pos >= S.size())  {
            result.push_back(row);
            return;
        }
        
        dfs(result, row, S, pos + 1);
        row.push_back(S[pos]);
        dfs(result, row, S, pos + 1);
    }
};
```
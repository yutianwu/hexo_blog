---
layout: post
title:  "DFS,图: Clone Graph"
date:   2014-11-1 21:04:20
categories: problems
---

>Clone an undirected graph. Each node in the graph contains a label and a list of its neighbors.

``` cpp
class Solution {
public:
    map<int, UndirectedGraphNode *> nodes;  //定义一个map来存储label与相对应的node指针
    
    //使用DFS来拷贝图
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        //！重要，如果node为空，直接返回空指针
        if (node == NULL)
            return NULL;
        
        //这里不能使用UndirectedGraphNode root(node->label)，然后返回地址，
        //这样会出现runtime error
        UndirectedGraphNode *root = new UndirectedGraphNode(node->label);
        nodes.insert(make_pair(node->label, root));
        for (int i = 0; i < node->neighbors.size(); i++) {
            int label = node->neighbors[i]->label;
            if (nodes.count(label) != 0) {
                //如果遍历过了，加入nieghbors
                root->neighbors.push_back(nodes[label]);
            } else {
                //如果没有遍历过，拷贝该节点
                root->neighbors.push_back(cloneGraph(node->neighbors[i]));
            }
        }
        return root;
    }
};
```
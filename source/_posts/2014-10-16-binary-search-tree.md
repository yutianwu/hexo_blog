---
layout: post
title:  "二叉搜索树(binary search tree)"
date:   2014-10-16 10:33:20
categories:   algorithms
tags: [二叉树]
---

##二叉搜索树

二叉搜索树又叫二叉查找树，是指一棵空树或者具有下列性质的二叉树：

+ 若任意节点的左子树不空，则左子树上所有结点的值均小于它的根结点的值；
+ 任意节点的右子树不空，则右子树上所有结点的值均大于它的根结点的值；
+ 任意节点的左、右子树也分别为二叉查找树。
+ 没有键值相等的节点（no duplicate nodes）。

二叉查找树相比于其他数据结构的优势在于查找、插入的时间复杂度较低。为O(log n)。但是二叉查找树
（以后简写为BST）也存在最坏的情况，那么操作的时间复杂度为O(n)。所以有很多对二叉树的改进版本，
比如AVL，红黑树。

二叉树一般使用数组来实现。下面我们约定二叉树的结构体。
```
    struct node {
        int key;
        node *lch, *rch;
    };
```

##BST的搜索
在二叉查找树p中查找x的过程为：

+ 若p是空树，则搜索失败，否则：
+ 若x等于p的根节点的数据域之值，则查找成功；否则：
+ 若x小于b的根节点的数据域之值，则搜索左子树；否则：
+ 查找右子树。

``` cpp
bool find(node *p, int x) {
    if (p == NULL)
        return false;
    else if (x == p->key)
        return true;
    else if (x < p->key)
        return find(p->lch, x);
    else
        return find(p->rch, x);
}
```

##在BST中插入节点
向一个二叉查找树p中插入一个值为x的节点的算法，过程为：

+ 若p是空树，新建一个空节点，将节点的键值赋值为x，返回该节点，否则：
+ 若x等于p的的键值，则返回，否则：
+ 若x小于p的键值，则把x插入到左子树中，否则：
+ 把x插入到右子树中。（新插入节点总是叶子节点）

``` cpp
node *insert(node *p, int x) {
    if (p == NULL) {
        node *q = new node;
        q->key = x;
        q->lch = q->rch = NULL;
        return q;
    }
    
    if (x < p->key)
        p->lch = insert(p->lch, x);
    else if (x > p->key)
        p->rch = insert(p->rch, x);
    return p;
}
```

##在BST中删除一个节点
在BST中删除一个节点时一般可以分为以下几种情况：

+ 如果需要删除的节点没有左子节点，那么把右子节点提上去
+ 如果需要删除的节点没有右子节点，那么把左子结点提上去
+ 如果不满足以上两种情况，那么把左子树中最大的节点提上去，也就是找出需要删除的节点的后继


``` cpp
node *remove(node *p, int x) {
    if (p == NULL)
        return NULL;
    else if (x < p->key) {
        p->lch = remove(p->lch, x);
        return p;
    }
    else if (x > p->key) {
        p->rch = remove(p->rch, x);
        return p;
    }
    
    //找到了，下面判断是属于哪种情况
    if (p->lch == NULL) {
        node *q = p->rch;
        delete p;
        return q;
    } else if (p->rch == NULL) {
        node *q = p->lch;
        delete p;
        return q;
    } else if (p->lch->rch == NULL) {
        node *q = p->lch;
        q->rch = p->rch;
        delete p;
        return q;
    } else {
        node *q;
        for (q = p->lch; q->rch->rch != NULL; q = q->rch);
        node *r = q->rch;
        q->rch = r->lch;
        p->key = r->key;
        delete r;
        return p;
    }
}
```

##完整的测试代码

下面是完整的测试代码，在中间加了一个中序遍历函数，来顺序输出各个节点的值：

``` cpp
#include <iostream>
using namespace std;

struct node {
    int key;
    node *lch, *rch;
};

node *insert(node *p, int x) {
    if (p == NULL) {
        node *q = new node;
        q->key = x;
        q->lch = q->rch = NULL;
        return q;
    }
    
    if (x < p->key)
        p->lch = insert(p->lch, x);
    else if (x > p->key)
        p->rch = insert(p->rch, x);
    return p;
}

bool find(node *p, int x) {
    if (p == NULL)
        return false;
    else if (x == p->key)
        return true;
    else if (x < p->key)
        return find(p->lch, x);
    else
        return find(p->rch, x);
}

node *remove(node *p, int x) {
    if (p == NULL)
        return NULL;
    else if (x < p->key) {
        p->lch = remove(p->lch, x);
        return p;
    }
    else if (x > p->key) {
        p->rch = remove(p->rch, x);
        return p;
    }
    
    //找到了，下面判断是属于哪种情况
    if (p->lch == NULL) {
        node *q = p->rch;
        delete p;
        return q;
    } else if (p->rch == NULL) {
        node *q = p->lch;
        delete p;
        return q;
    } else if (p->lch->rch == NULL) {
        node *q = p->lch;
        q->rch = p->rch;
        delete p;
        return q;
    } else {
        node *q;
        for (q = p->lch; q->rch->rch != NULL; q = q->rch);
        node *r = q->rch;
        q->rch = r->lch;
        p->key = r->key;
        delete r;
        return p;
    }
}

void inorderTraversal(node *p) {
    if (p == NULL)
        return;
    
    inorderTraversal(p->lch);
    cout<<p->key<<endl;
    inorderTraversal(p->rch);
}

int main () {
    node *p = NULL; //为节点指针赋空值很重要！！！！
    for (int i = 0; i < 5; i++) {
        int key;
        cin>>key;
        p = insert(p, key);
    }
    inorderTraversal(p);
    p = remove(p, 1);
    inorderTraversal(p);
    p = remove(p, 5);
    inorderTraversal(p);
    cout<<find(p, 1)<<endl;
    cout<<find(p, 2)<<endl;
}
```
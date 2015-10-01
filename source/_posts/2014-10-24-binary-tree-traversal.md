---
layout: post
title:  "二叉树的普通非递归遍历"
date:   2014-10-23 21:16:20
categories:   algorithms
tags: [二叉树]
---

这里给出二叉树的普通非递归遍历：

``` cpp
#include <iostream>
#include <queue>
#include <stack>
using namespace std;

//二叉树的节点
struct Node {
    int val;
    Node *lch, *rch;
    Node(int x):val(x), lch(NULL), rch(NULL) {}
};

//前序遍历
/*
 前序遍历就是先访问当前节点，然后依次访问左子结点，右子节点
 所以很简单，我们就直接用一个栈先压入右子节点，然后压入左子结点就可以了
 */
void preorderTraversal(Node *root) {
    stack<Node *> s;
    Node *cur;          //当前访问节点
    
    if (root != NULL)   //如果根节点不为空，压栈
        s.push(root);
    
    while (!s.empty()) {    //如果栈不为空
        cur = s.top();      //输出当前访问节点
        s.pop();
        cout<<cur->val;
        
        //依次将右子节点，左子结点压栈
        if (cur->rch != NULL)
            s.push(cur->rch);
        if (cur->lch != NULL)
            s.push(cur->lch);
    }
}


//后序遍历的非递归实现
/*
 后序遍历就是先访问节点的左子结点和右子节点，再访问当前节点
 如果不用递归，我们就可以用栈，我们可以依次将当前节点、右子节点和左子结点入栈，
 然后再弹栈
 但是这里又有另一个问题，如果我们访问到了一个节点，可是我们不知道到底是应该将它的两个子节点
 压栈还是该将它弹出。因此就需要一个辅助的节点来记录上一个弹出的节点，如果被弹出的上一个节点
 是它的一个子节点，那么说明前面已经将该节点的子节点压栈了，现在该将它弹出。问题就解决了。
 */
void postorderTraversal(Node *root) {
    stack<Node *> s;
    Node *pre = NULL;   //已经输出过的前一个节点
    Node *cur = NULL;   //当前访问的节点
    
    if (root != NULL)
        s.push(root);       //首先将根节点压栈
    
    while (!s.empty()) {
        cur = s.top();  //获取栈顶的节点
        
        if ((cur->lch == NULL && cur->rch == NULL) ||
            (pre != NULL && (cur->lch == pre || cur->rch == pre))) {
            //如果当前的节点既没有左子结点又没有右子节点
            //或者当前节点的子节点已经被输出过，即已经是第二次经过该节点了
            //输出当前节点，并且将当前节点赋给pre
            cout<<cur->val;
            s.pop();
            pre = cur;
        } else {
            //依次将右子节点和左子结点入栈
            if (cur->rch != NULL)
                s.push(cur->rch);

            if (cur->lch != NULL)
                s.push(cur->lch);
        }
    }
}

//中序遍历
/*
 中序遍历不能像后序遍历一样记录前一个访问的节点，因为前一个访问的节点并不是要访问的节点的
 直接子节点，所以又得换种方法：
 我们用一个节点记录当前访问的节点，然后用一个栈记录上一个访问过的节点。如果当前的节点不为空，
 那么向下访问左子结点。
 如果当前访问的节点为空，说明上一个访问的节点已经没有左子结点了，那么输出上一个节点，
 然后访问上一个节点的右子节点。
 */
void inorderTraversal(Node *root) {
    stack<Node *> s;
    Node *cur = root;       //当前访问节点
    
    while (!s.empty() || cur != NULL) { //如果当前访问节点与栈都不为空
        if (cur != NULL) {
            //如果当前节点不为空，那么继续往下访问左子结点，并将当前节点压栈
            s.push(cur);
            cur = cur->lch;
        } else {
            //如果当前节点为空，那么从栈顶弹出节点，并输出该节点，然后继续往下访问
            //右子节点
            cur = s.top();
            s.pop();
            
            cout<<cur->val;
            cur = cur->rch;
        }
    }
}

//层序遍历
//层序遍历跟前序遍历差不多，就是把栈换成了队列，然后左右子节点入队的顺序交换了一下
void levelorderTraversal(Node *root) {
    queue<Node *> que;
    Node *cur;
    
    if (root != NULL)
        que.push(root);
    
    while (!que.empty()) {
        cur = que.front();
        cout<<cur->val;
        que.pop();
        
        if (cur->lch != NULL)
            que.push(cur->lch);
        if (cur->rch != NULL)
            que.push(cur->rch);
    }
}

int main() {
    Node root(1);
    Node c1(2);
    Node c2(3);
    Node c3(4);
    Node c4(5);
    Node c5(6);
    root.lch = &c1;
    root.rch = &c2;
    c1.lch = &c3;
    c1.rch = &c4;
    c2.lch = &c5;

    preorderTraversal(&root);
    postorderTraversal(&root);
    inorderTraversal(&root);
    levelorderTraversal(&root);
}
```
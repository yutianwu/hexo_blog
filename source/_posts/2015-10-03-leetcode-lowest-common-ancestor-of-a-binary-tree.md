---
layout: post
title:  "LeetCode: Lowest Common Ancestor of a Binary Tree"
categories: LeetCode
date:   2015-10-03 21:49:20
---

> Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

想法还是很简单，如果要判断当前节点是否是两个节点$p,q$的最低公共父节点，（假设我们是从上往下开始判断的），那么如果当前节点与两个节点之一相等，那么当前节点可能是这两个节点的最低公共父节点，返回；如果不相等，那么就在当前节点的两个子节点中找；如果再两个子节点中都找到了可能的最低公共父节点，那么当前节点即是结果，否则返回两个子节点找到得结果。

代码如下：

``` cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (root == NULL)
            return NULL;
        if (root == p || root == q)
            return root;
        
        TreeNode *left = lowestCommonAncestor(root->left, p, q);
        TreeNode *right = lowestCommonAncestor(root->right, p, q);
        if (left != NULL && right != NULL)
            return root;
        else if (left == NULL && right == NULL)
            return NULL;
            
        return left != NULL ? left : right;
    }
};```


---
layout: post
title:  "重建二叉树"
date:   2014-11-21 18:08:20
categories:   algorithms
tags: [二叉树]
---

重建二叉树就是给定二叉树的两种遍历的结果，比如前序遍历，中序遍历，然后根据这两种遍历结果来重建二叉树。

比如说给定一个二叉树

+ 前序遍历：$[1, 2, 4, 5, 3, 6, 7]$
+ 中序遍历：$[4, 2, 5, 1, 6, 3, 7]$

然后根据这两个遍历结果重建二叉树。

重建二叉树的思想很简单，比如说根据前序遍历的结果，我们能够知道$1$为树的根节点，但是我们不能知道它的两个子树是哪些节点；但是根据中序遍历，我们就知道，在根节点左边的为左子树，根节点右边的为右子树，所以左子树为$[4,2,5]$,右子树为$[6, 3, 7]$.然后我们再递归的重建两个子树就可以了。

代码实现如下，方法很简单，但是值得学习的就是在这两个方法中迭代器的漂亮使用以及代码的精简实现。

###根据前序遍历和中序遍历的结果构建二叉树

``` cpp
class Solution {
public:
    TreeNode *buildTree(vector<int> &preorder, vector<int> &inorder) {
        return buildTree(begin(preorder), end(preorder),
                         begin(inorder), end(inorder));
    }
    
    template <typename InputIterator>
    TreeNode *buildTree(InputIterator pre_first, InputIterator pre_last,
                        InputIterator in_first, InputIterator in_last) {
     
        //如果序列中已经没有元素,那么返回空指针
        if (pre_first == pre_last) return nullptr;
        if (in_first == in_last) return nullptr;
        
        auto root = new TreeNode(*pre_first);
        auto in_root_pos = find(in_first, in_last, *pre_first);
        auto left_size = distance(in_first, in_root_pos);
        
        //在这里需要注意的是，序列的范围都是一个左闭右开的区间，即[first, last),
        root->left = buildTree(next(pre_first), next(pre_first, left_size + 1),
                               in_first, in_root_pos);
        
        root->right = buildTree(next(pre_first, left_size + 1),
                                pre_last, next(in_root_pos), in_last);
        
        return root;
    }
};
```

###根据中序遍历和后序遍历的结果构建二叉树
``` cpp
class Solution {
public:
    TreeNode *buildTree(vector<int> &inorder, vector<int> &postorder) {
        return buildTree(begin(inorder), end(inorder),
                         begin(postorder), end(postorder));
    }
    
    template <typename InputIterator>
    TreeNode *buildTree(InputIterator in_first, InputIterator in_last,
                        InputIterator post_first, InputIterator post_last) {
     
        //如果序列中已经没有元素,那么返回空指针
        if (in_first == in_last) return nullptr;
        if (post_first == post_last) return nullptr;
        
        auto val = *prev(post_last);
        auto root = new TreeNode(val);
        auto in_root_pos = find(in_first, in_last, val);
        auto left_size = distance(in_first, in_root_pos);
        auto post_left_last = next(post_first, left_size);
        
        //在这里需要注意的是，序列的范围都是一个左闭右开的区间，即[first, last),
        root->left = buildTree(in_first, in_root_pos,
                               post_first, post_left_last);
        
        root->right = buildTree(next(in_root_pos), in_last,
                                post_left_last, prev(post_last));
        
        return root;
    }
};
```
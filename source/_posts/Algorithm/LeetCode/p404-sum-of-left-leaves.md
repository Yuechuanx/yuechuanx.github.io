---
title: 404. Sum of Left Leaves
categories:
  - Algorithm
  - LeetCode
tags:
  - algorithm
---

~~~cpp
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
   int sumOfLeftLeaves(TreeNode* root) {
       if(!root) return 0;
       if(root ->left && !root -> left ->left && !root ->left ->right) {
           return root -> left -> val + sumOfLeftLeaves(root -> right);
       }
       return sumOfLeftLeaves(root -> left) + sumOfLeftLeaves(root -> right);
   }
};
~~~
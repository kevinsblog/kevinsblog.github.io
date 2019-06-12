---
layout: post
title: LeetCode专题-广度优先搜索
---

## 429. N-ary Tree Level Order Traversal
Easy

Given an n-ary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example, given a 3-ary tree:

 
[https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png]
 
We should return its level order traversal:

[
     [1],
     [3,2,4],
     [5,6]
]

Note:

    The depth of the tree is at most 1000.
    The total number of nodes is at most 5000.

题目大意：对一个N叉树实现按层遍历，将遍历结果返回。

解题思路：广度优先搜索。

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/
class Solution {
public:
    vector<vector<int>> levelOrder(Node* root) {
        //the final result
        vector<vector<int>> ans;
        if (root == nullptr) {
            return ans;
        }

        queue<Node*> Q;
        Q.push(root); //start from root
        int lev = 0;
        while (Q.size() > 0) {
            size_t num = Q.size();
            if (num > 0) {
                //create new level for traversal
                ans.push_back(vector<int>{});
            }
            //visit all node of current level
            while (num-- > 0) {
                ans[lev].push_back(Q.front()->val);
                //push node of next level to the queue
                for (auto child : Q.front()->children) {
                    Q.push(child);
                }
                Q.pop();
            }
            lev++;
        }

        return move(ans);       
    }
};
```
测试一下，

```
Success
Details
Runtime: 156 ms, faster than 83.54% of C++ online submissions for N-ary Tree Level Order Traversal.
Memory Usage: 33.8 MB, less than 62.80% of C++ online submissions for N-ary Tree Level Order Traversal.
```
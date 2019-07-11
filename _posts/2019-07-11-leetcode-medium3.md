---
layout: post
title: LeetCode专题 - 复习题集合(二)
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 257. 二叉树的所有路径

给定一个二叉树，返回所有从根节点到叶子节点的路径。

说明: 叶子节点是指没有子节点的节点。

示例:

```
输入:

   1
 /   \
2     3
 \
  5

输出: ["1->2->5", "1->3"]
```
解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3

{% highlight c++ linenos %}
class Solution {
public:
    void dfs(TreeNode* root, vector<int> &path, vector<vector<int>> &seqs){
        if(!root){
            return;
        }
        
        path.push_back(root->val);
        if(!root->left && !root->right){
            seqs.push_back(path);
            path.pop_back();
            return;
        }
        
        dfs(root->left, path, seqs);
        dfs(root->right, path, seqs);
        path.pop_back();
    }
    
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> ans;
        vector<vector<int>> seqs;
        vector<int> path;
        dfs(root, path, seqs);
        for(auto & seq : seqs){
            string s;
            for(int i = 0; i < seq.size(); i++){
                if(i != seq.size() - 1){
                    s += to_string(seq[i]) + "->";
                }else{
                    s += to_string(seq[i]);
                }
            }
            
            if(!s.empty()){
                ans.push_back(s);
            }
        }
        
        return move(ans);
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :4 ms, 在所有 C++ 提交中击败了95.14% 的用户
内存消耗 :10.7 MB, 在所有 C++ 提交中击败了97.52%的用户
```

{% highlight c++ linenos %}
{% endhighlight %}
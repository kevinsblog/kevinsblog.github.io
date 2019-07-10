---
layout: post
title: LeetCode专题 - Medium题集合(二)
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 226. 翻转二叉树

翻转一棵二叉树。

```
示例：

输入：

     4
   /   \
  2     7
 / \   / \
1   3 6   9

输出：

     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

备注:
这个问题是受到 Max Howell 的 原问题 启发的 ：

    >谷歌：我们90％的工程师使用您编写的软件(Homebrew)，但是您却无法在面试时在白板上写出翻转二叉树这道题，这太糟糕了。

{% highlight c++ linenos %}
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
    //pre-order traversal
    void mirror(TreeNode *root){
        if(!root || (!root->left && !root->right)){
            return;
        }
        
        swap(root->left, root->right);
        if(root->left)
            mirror(root->left);
        if(root->right)
            mirror(root->right);

    }
    
    TreeNode* invertTree(TreeNode* root) {
        mirror(root);
        return root;
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :4 ms, 在所有 C++ 提交中击败了88.67% 的用户
内存消耗 :9.2 MB, 在所有 C++ 提交中击败了32.01%的用户
```

# 138. 复制带随机指针的链表

给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。

要求返回这个链表的深拷贝。 

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/23/1470150906153-2yxeznm.png)
```
示例：

输入：
{"$id":"1","next":{"$id":"2","next":null,"random":{"$ref":"2"},"val":2},"random":{"$ref":"2"},"val":1}

解释：
节点 1 的值是 1，它的下一个指针和随机指针都指向节点 2 。
节点 2 的值是 2，它的下一个指针指向 null，随机指针指向它自己。

```
提示：

    你必须返回给定头的拷贝作为对克隆列表的引用。

{% highlight c++ linenos %}
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;

    Node() {}

    Node(int _val, Node* _next, Node* _random) {
        val = _val;
        next = _next;
        random = _random;
    }
};
*/
class Solution {
    unordered_map<Node*, Node*> mp;
public:  
    Node* copyRandomList(Node* head) {
       if(!head){
           return nullptr;
       }
       if(mp.find(head) == mp.end()){
           mp[head] = new Node(head->val);
           mp[head]->next = copyRandomList(head->next);
           mp[head]->random = copyRandomList(head->random);
       }
       return mp[head];
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :64 ms, 在所有 C++ 提交中击败了53.88% 的用户
内存消耗 :22.2 MB, 在所有 C++ 提交中击败了5.58%的用户
```

{% highlight c++ linenos %}
{% endhighlight %}
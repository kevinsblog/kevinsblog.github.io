---
layout: post
title: LeetCode专题-图
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 133. Clone Graph

Medium

Given a reference of a node in a connected undirected graph, return a deep copy (clone) of the graph. Each node in the graph contains a val (int) and a list (List[Node]) of its neighbors.

![图](https://assets.leetcode.com/uploads/2019/02/19/113_sample.png)

```
Example:

Input:
{"$id":"1","neighbors":[{"$id":"2","neighbors":[{"$ref":"1"},{"$id":"3","neighbors":[{"$ref":"2"},{"$id":"4","neighbors":[{"$ref":"3"},{"$ref":"1"}],"val":4}],"val":3}],"val":2},{"$ref":"4"}],"val":1}
```

Explanation:
Node 1's value is 1, and it has two neighbors: Node 2 and 4.
Node 2's value is 2, and it has two neighbors: Node 1 and 3.
Node 3's value is 3, and it has two neighbors: Node 2 and 4.
Node 4's value is 4, and it has two neighbors: Node 1 and 3.

Note:
    The number of nodes will be between 1 and 100.
    The undirected graph is a simple graph, which means no repeated edges and no self-loops in the graph.
    Since the graph is undirected, if node p has node q as neighbor, then node q must have node p as neighbor too.
    You must return the copy of the given node as a reference to the cloned graph.

题目大意：给一个图，要求返回一个此图的深复制。

解题思路：图的结构不变，通过一个map保存旧指针到新指针的映射关系。

{% highlight c++ linenos %}
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;

    Node() {}

    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/
class Solution {
public:
    Node* getPtr(unordered_map<Node*, Node*> &mpPtr, Node * const oldPtr){
        if(oldPtr == nullptr){
            return nullptr;
        }
        if(mpPtr.count(oldPtr) == 0){
            //create the node if it is not yet build
            mpPtr[oldPtr] = new Node();
            mpPtr[oldPtr]->val = oldPtr->val;
            for(auto nh : oldPtr->neighbors){
                mpPtr[oldPtr]->neighbors.push_back(getPtr(mpPtr,nh));
            }
        }
        return mpPtr[oldPtr];
    }

    Node* cloneGraph(Node* node) {
        unordered_map<Node*, Node*> mpPtr;
        Node *nnode = getPtr(mpPtr, node);
        return nnode;
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 24 ms, faster than 88.55% of C++ online submissions for Clone Graph.
Memory Usage: 16.7 MB, less than 21.53% of C++ online submissions for Clone Graph.
```
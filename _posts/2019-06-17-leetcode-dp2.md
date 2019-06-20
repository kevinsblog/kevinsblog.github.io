---
layout: post
title: LeetCode专题-动态规划(二)
tags: [Leetcode]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

<span id="jump120">Medium</span>

# 120. Triangle

Medium

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

```
For example, given the following triangle

[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]

The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).
```

Note:

Bonus point if you are able to do this using only O(n) extra space, where n is the total number of rows in the triangle.

题目大意：一个等边三角形内，只能在临近元素中向下移动，要求找到一条路径，使得其路径和最小。

解题思路：用dp来做，但是题目要求只能用O(n)的空间复杂度，因此记录路径和的dp是一个长度为n的数组，而不能是mxn的二维数组。

```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        int m = triangle[n-1].size();
        // traverse from bottom to top
        vector<int> dp = triangle[n-1];
        for(int i = n - 2; i >= 0; i--){
            for(int j = 0; j < m; j++){
                //min path of the two
                dp[j] = min(dp[j], dp[j+1]) + triangle[i][j];
            }
        }
        return dp[0];        
    }
};
```
测试一下，
```
Success
Details
Runtime: 4 ms, faster than 98.48% of C++ online submissions for Triangle.
Memory Usage: 9.9 MB, less than 42.74% of C++ online submissions for Triangle.
```
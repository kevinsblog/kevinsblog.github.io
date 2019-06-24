---
layout: post
title: LeetCode专题-动态规划(二)
tags: [Leetcode, C/C++]
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

# 139. Word Break

Medium

Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words.

Note:

    The same word in the dictionary may be reused multiple times in the segmentation.
    You may assume the dictionary does not contain duplicate words.

```
Example 1:

Input: s = "leetcode", wordDict = ["leet", "code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".

Example 2:

Input: s = "applepenapple", wordDict = ["apple", "pen"]
Output: true
Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".
             Note that you are allowed to reuse a dictionary word.

Example 3:

Input: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
Output: false
```

题目大意：给一个字符串和一个字符串字典，要求判断字符串能否用字典中的字符串完全分割。

解题思路：用动态规划，递推公式是，有字符串位置j和i，若s\[0:j\]已经确定可以分割，s\[j:i\]也存在在字典中，则s\[0:i\]也可以分割。

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& dict) {
        vector<bool> dp(s.length() + 1, false);
        dp[0] = true;
        //i is the end index of substr, exclude the end element
        for (int i = 1; i < dp.size(); i++) {
            //j is the start index of substr
            for (int j = i - 1; j >= 0; j--) {
                //if string is valid in index j, and [j:i] is in dict
                //  dp[i] is also valid
                if (dp[j] && 
                   find(dict.begin(), dict.end(), s.substr(j, i - j)) != dict.end()) {
                    dp[i] = true;
                    break; 
                }
            }
        }
        return dp[s.length()];        
    }
};
```
测试一下，
```
Success
Details
Runtime: 4 ms, faster than 97.49% of C++ online submissions for Word Break.
Memory Usage: 10.6 MB, less than 74.84% of C++ online submissions for Word Break.
```

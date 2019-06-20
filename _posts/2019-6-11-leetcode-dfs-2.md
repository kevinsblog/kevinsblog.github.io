---
layout: post
title: LeetCode专题-深度优先搜索(二)
tags: [Leetcode]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

## 51. N-Queens

Hard

The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.

![八皇后](https://assets.leetcode.com/uploads/2018/10/12/8-queens.png)

Given an integer n, return all distinct solutions to the n-queens puzzle.

Each solution contains a distinct board configuration of the n-queens' placement, where 'Q' and '.' both indicate a queen and an empty space respectively.

```
Example:

Input: 4
Output: [
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]
```
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above.

题目大意：在nxn的棋盘上放置n个国际象棋的皇后，要求皇后之间不能够相互攻击到。

解题思路：利用DFS，从第一行开始，放置皇后在安全的位置。

```c++
class Solution {
public:
    // check if put a queen at [row, col] is valid
    bool isValid(const vector<string> &cur, int row, int col){
        // check if queen exists in the same col
        for(int i = 0; i < row; i++){
            if(cur[i][col] == 'Q'){
                return false;
            }
        }

        // check if queen exists in up-left diag
        for(int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--){
            if(cur[i][j] == 'Q'){
                return false;
            }
        }

        // check if queen exists in up-right diag
        for(int i = row - 1, j = col + 1; i >= 0 && j < cur.size(); i--, j++){
            if(cur[i][j] == 'Q'){
                return false;
            }
        }

        return true;
    }

    void solveNQueensDFS(vector<string> &cur, int row, vector<vector<string>> &ans){
        if(row == cur.size()){
            //if all queens have been positioned
            ans.push_back(cur);
            return;
        }

        // try to place queen at one col of current row
        for(int col = 0; col < cur.size(); col++){
            if(isValid(cur, row, col)){
                cur[row][col] = 'Q';
                solveNQueensDFS(cur, row + 1, ans);
                cur[row][col] = '.';
            }
        }
    }

    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        vector<string>  cur(n, string(n, '.'));
        solveNQueensDFS(cur, 0, ans);
        return move(ans);
    }
};
```
测试一下
```
Success
Details
Runtime: 8 ms, faster than 92.21% of C++ online submissions for N-Queens.
Memory Usage: 10.1 MB, less than 66.69% of C++ online submissions for N-Queens.
```
<span id="jump52"></span>

## 52. N-Queens II

Hard

The n-queens puzzle is the problem of placing n queens on an n×n chessboard such that no two queens attack each other.

Given an integer n, return the number of distinct solutions to the n-queens puzzle.

Example:

Input: 4
Output: 2
Explanation: There are two distinct solutions to the 4-queens puzzle as shown below.
[
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]

题目大意：和上题相比稍加修改，要求求出可能的解法个数。

解题思路：思路与上题相同。

```c++
class Solution {
public:
    int totalNQueens(int n) {
        int cnt = 0;
        vector<string>  cur(n, string(n, '.'));
        solveNQueensDFS(cur, 0, cnt);
        return cnt;        
    }
    
    bool isValid(const vector<string> &cur, int row, int col){
        for(int i = 0; i < row; i++){
            if(cur[i][col] == 'Q'){
                return false;
            }
        }

        for(int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--){
            if(cur[i][j] == 'Q'){
                return false;
            }
        }

        for(int i = row - 1, j = col + 1; i >= 0 && j < cur.size(); i--, j++){
            if(cur[i][j] == 'Q'){
                return false;
            }
        }

        return true;
    }

    void solveNQueensDFS(vector<string> &cur, int row, int &cnt){
        if(row == cur.size()){
            cnt++;
            return;
        }

        for(int col = 0; col < cur.size(); col++){
            if(isValid(cur, row, col)){
                cur[row][col] = 'Q';
                solveNQueensDFS(cur, row + 1, cnt);
                cur[row][col] = '.';
            }
        }
    }
  
};
```
测试一下，
```
Success
Details
Runtime: 0 ms, faster than 100.00% of C++ online submissions for N-Queens II.
Memory Usage: 8.5 MB, less than 39.40% of C++ online submissions for N-Queens II.
```
<span id="jump77"></span>

##77. Combinations

Medium

Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.

```
Example:

Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

题目大意：从1-n中找k个数形成组合，要求列出所有可能的组合。

解题思路：用DFS穷举所有的组合，保存符合条件的。

```c++
class Solution {
public:
    void combineDFS(int start, const int k, const int n, vector<int> &path, vector<vector<int>> &ans){
        if(path.size() == k){
            ans.push_back(path);
            return;
        }

        for(int i = start; i <= n; i++){
            path.push_back(i);
            combineDFS(i + 1, k, n, path, ans);
            path.pop_back();
        }
    }    
    
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        vector<int> path;
        combineDFS(1, k, n, path, ans);
        return move(ans);        
    }
};
```
测试一下，
```
Success
Details
Runtime: 84 ms, faster than 70.48% of C++ online submissions for Combinations.
Memory Usage: 11.8 MB, less than 67.96% of C++ online submissions for Combinations.
```

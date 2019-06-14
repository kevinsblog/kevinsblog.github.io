---
layout: post
title: LeetCode专题-深度优先搜索(三)
tags: [Leetcode]
bigimg: /img/path.jpg
comments: true
---

# 目录

[79. Word Search](#jump79)

[212. Word Search II](#jump212)

<span id="jump79">Medium</span>

#79. Word Search

Medium

Given a 2D board and a word, find if the word exists in the grid.

The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.
```
Example:

board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
```

Given word = "ABCCED", return true.
Given word = "SEE", return true.
Given word = "ABCB", return false.

题目大意：给出一个字母矩阵和一个单词，要求判断在矩阵中是否存在一条路径，路径字母的组合是这个单词。

解题思路：首先扫描矩阵，得到单词路径的所有可能起点，然后用DFS找出路径。

```c++
class Solution {
public:
    bool existDFS(const vector<vector<char>> &board, const string &word, vector<pair<int, int>> &path){
        //search completed as matched
        if(path.size() == word.size()){
            return true;
        }

        //search next character in four neighbor
        int offset[4][2] = {{-1, 0}, {0, -1}, {1, 0}, {0, 1}};
        for(int i = 0; i < 4; i++){
            int x = path.back().first + offset[i][0],
                y = path.back().second + offset[i][1];
            //skip if neighbor pos in invalid
            if(x < 0 || x >= board.size() || y < 0 || y >= board.at(0).size()){
                continue;
            }
            //skip if neighbor char is not match
            if(board[x][y] != word[path.size()]){
                continue;   
            }
            auto pos = pair<int, int>(x, y);
            //skip if neighbor pos already in path and searched
            if(find(path.begin(), path.end(), pos) != path.end()){
                continue;
            }
            path.push_back(pos);
            //match and advance to search next word
            if(existDFS(board, word, path)){
                return true;
            }
            path.pop_back();
        }

        return false;
    }
    bool exist(vector<vector<char>> &board, string word){
        unordered_map<char, vector<pair<int, int>>> start_at;
        for(int i = 0; i < board.size(); i++){
            for(int j = 0; j < board[i].size(); j++){
                start_at[board[i][j]].push_back(pair<int, int>(i, j));
            }
        }

        for(auto & start : start_at[*word.begin()]){
            vector<pair<int, int>> path{start};
            if(existDFS(board, word, path)){
                return true;
            }
        }

        return false;
    }
};
```

测试一下,
```
Success
Details
Runtime: 72 ms, faster than 51.46% of C++ online submissions for Word Search.
Memory Usage: 14.5 MB, less than 57.58% of C++ online submissions for Word Search.
```
<span id="jump212">Medium</span>

# 212. Word Search II

Hard

Given a 2D board and a list of words from the dictionary, find all words in the board.

Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

```
Example:

Input: 
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath","pea","eat","rain"]

Output: ["eat","oath"]
```

Note:
    All inputs are consist of lowercase letters a-z.
    The values of words are distinct.

题目大意：在上题的基础上，要求找出一个字典内所有能找到矩阵路径的单词。

解题思路：本来以为在上题的基础上，稍作修改就可以，但是一些测试用例中单词存在大量相同的前缀，导致计算超时。所以这道题还是应该用前缀表达树做。

```c++
class Solution {
public:
    bool existDFS(const vector<vector<char>> &board, const string &word, vector<pair<int, int>> &path){
        if(path.size() == word.size()){
            return true;
        }

        int offset[4][2] = {{-1, 0}, {0, -1}, {1, 0}, {0, 1}};
        for(int i = 0; i < 4; i++){
            int x = path.back().first + offset[i][0],
                y = path.back().second + offset[i][1];
            if(x < 0 || x >= board.size() || y < 0 || y >= board.at(0).size()){
                continue;
            }
            if(board[x][y] != word[path.size()]){
                continue;   
            }
            auto pos = pair<int, int>(x, y);
            if(find(path.begin(), path.end(), pos) != path.end()){
                continue;
            }
            path.push_back(pos);
            if(existDFS(board, word, path)){
                return true;
            }
            path.pop_back();
        }

        return false;
    }

    vector<string> findWords(vector<vector<char>>& board, vector<string>& words) {
        unordered_map<char, vector<pair<int, int>>> start_at;
        for(int i = 0; i < board.size(); i++){
            for(int j = 0; j < board[i].size(); j++){
                start_at[board[i][j]].push_back(pair<int, int>(i, j));
            }
        }

        vector<string> ans;
        //traversal all words in dict
        for(auto & word : words){
            for(auto & start : start_at[*word.begin()]){
                vector<pair<int, int>> path{start};
                if(existDFS(board, word, path)){
                    //skip duplicate ones
                    if(find(ans.begin(), ans.end(), word) == ans.end())
                        ans.push_back(word);
                }
            }        
        }

        return move(ans);        
    }
};
```
测试一下
```
Time Limit Exceeded
Details
Last executed input
[["a","a","a","a"],["a","a","a","a"],["a","a","a","a"],["a","a","a","a"],["b","c","d","e"],["f","g","h","i"],["j","k","l","m"],["n","o","p","q"],["r","s","t","u"],["v","w","x","y"],["z","z","z","z"]]
["aaaaaaaaaaaaaaaa","aaaaaaaaaaaaaaab",....
```
---
layout: post
title: LeetCode专题-深度优先搜索(三)
tags: [Leetcode]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 79. Word Search

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
Given word = "ABCCED", return true.
Given word = "SEE", return true.
Given word = "ABCB", return false.
```

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
        int offset[4][2] = { {-1, 0}, {0, -1}, {1, 0}, {0, 1} };
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
<span id="jump212"></span>

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

        int offset[4][2] = { {-1, 0}, {0, -1}, {1, 0}, {0, 1} };
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
<span id="jump93"></span>

# 93. Restore IP Addresses

Medium

Given a string containing only digits, restore it by returning all possible valid IP address combinations.

```
Example:

Input: "25525511135"
Output: ["255.255.11.135", "255.255.111.35"]
```

题目大意：一个字符串，包含一个ip地址，要求将ip地址解析出来。

解题思路：用dfs，比较麻烦的是，测试用例千奇百怪，因此我们需要进行很多剪枝，太长的或者太短的ip地址不合法，连续0需要剪枝。

```c++
class Solution {
public:
    void restoreIpAddressesDFS(const string &s, int start, vector<int> &path, vector<string> &ans){
        if(start == s.size()){
            if(path.size() == 4){
                string ip;
                for(int i = 0; i < path.size(); i++){
                    ip += to_string(path[i]);
                    if(i != path.size() - 1)
                        ip += ".";
                }
                ans.push_back(ip);
            }
            return;
        }

        //quit if the remain string is too short or too long
        if(s.size() - start < 4 - path.size())
            return;

        if(s.size() - start > (4 - path.size())*3)
            return;

        int num = 0;
        //one num has at most 3 bits
        for(int i = start; i < start + 3; i++){
            if(i >= s.size()) //quit if reach the end
                break;

            int res = num*10 + (s[i] - '0');
            if(res > 255 || res < 0) //quit if num invalid
                break;

            path.push_back(res);
            restoreIpAddressesDFS(s, i + 1, path, ans);
            path.pop_back();
            num = res;
            
            // no more loop if num is zero
            if(num == 0)
                break;
        }
    }

    vector<string> restoreIpAddresses(string s) {
        vector<string> ans;
        vector<int> ip;
        restoreIpAddressesDFS(s, 0, ip, ans);
        return move(ans);
    }
};
```
测试一下，
```
Success
Details
Runtime: 0 ms, faster than 100.00% of C++ online submissions for Restore IP Addresses.
Memory Usage: 8.8 MB, less than 30.52% of C++ online submissions for Restore IP Addresses.
```
<span id="jump91"></span>

# 91. Decode Ways

Medium

A message containing letters from A-Z is being encoded to numbers using the following mapping:

'A' -> 1
'B' -> 2
...
'Z' -> 26

Given a non-empty string containing only digits, determine the total number of ways to decode it.
```
Example 1:

Input: "12"
Output: 2
Explanation: It could be decoded as "AB" (1 2) or "L" (12).

Example 2:

Input: "226"
Output: 3
Explanation: It could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
```

题目大意：一列数字，要将它解析成A-Z组成的字符串。

解题思路：用DFS，但是面对长测试集合无法通过。

```c++
class Solution {
public:
    void numDecodingsDFS(const string &s, int start, vector<char> &path, vector<vector<char>> &ans){
        if(start == s.size()){
            ans.push_back(path);
            return;
        }

        int num = 0;
        for(int i = start; i < start + 2; i++){
            int res = num*10 + s[i] - '0';
            if(res < 1 || res > 26){
                break;
            }

            path.push_back('A' + res);
            numDecodingsDFS(s, i+1, path, ans);
            path.pop_back();

            num = res;
            if(num == 0){
                break;
            }
        }
    }

    int numDecodings(string s) {
        vector<vector<char>> ans;
        vector<char> path;
        numDecodingsDFS(s, 0, path, ans);
        return ans.size();
    }
};
```
测试一下
```
Time Limit Exceeded
Details
Playground Debug
Last executed input
"9371597631128776948387197132267188677349946742344217846154932859125134924241649584251978418763151253"
```
<span id="jump131"></span>

# 131. Palindrome Partitioning

Medium

Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

```
Example:

Input: "aab"
Output:
[
  ["aa","b"],
  ["a","a","b"]
]
```

### 题目大意

{: .box-note}
**-** 将一个字符串划分成一系列子字符串，每个子字符串都是回文。

### 解题思路

{: .box-note}
**-** 用DFS，搜索所有符合条件的路径。

{% highlight c++ linenos %}
class Solution {
public:
    void partitionDFS(const string &s, int start, vector<string> &path,vector<vector<string>> &ans){
        //judge if word[start:end] is a valid palindrome
        auto isPalindrome = [](const string &word, int start, int end){
            while(start < end){
                if(word[start] != word[end])
                    return false;
                start++;
                end--;
            }
            return true;
        };

        //parition done
        if(start == s.size()){
            ans.push_back(path);
            return;
        }

        for(int i = start; i < s.size(); i++){
            //skip if current parition is not palindrome
            if(!isPalindrome(s, start, i))
                continue;

            path.push_back(s.substr(start, i - start + 1));
            partitionDFS(s, i + 1, path, ans); //search for following sub str
            path.pop_back();
        }
    }

    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        vector<string> path;
        partitionDFS(s, 0, path, ans);
        return move(ans);
    }        
};
{% endhighlight %}

测试一下，
```
Success
Details
Runtime: 8 ms, faster than 99.48% of C++ online submissions for Palindrome Partitioning.
Memory Usage: 12.6 MB, less than 88.25% of C++ online submissions for Palindrome Partitioning.
```
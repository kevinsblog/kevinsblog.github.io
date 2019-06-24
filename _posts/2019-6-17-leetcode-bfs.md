---
layout: post
title: LeetCode专题-广度优先搜索
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 429. N-ary Tree Level Order Traversal

Easy

Given an n-ary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).

For example, given a 3-ary tree:

 
![3-ary tree](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)
 
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

{% highlight c++ linenos %}
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
{% endhighlight %}
测试一下，

```
Success
Details
Runtime: 156 ms, faster than 83.54% of C++ online submissions for N-ary Tree Level Order Traversal.
Memory Usage: 33.8 MB, less than 62.80% of C++ online submissions for N-ary Tree Level Order Traversal.
```

# 127. Word Ladder

Medium

Given two words (beginWord and endWord), and a dictionary's word list, find the length of shortest transformation sequence from beginWord to endWord, such that:

    Only one letter can be changed at a time.
    Each transformed word must exist in the word list. Note that beginWord is not a transformed word.

Note:

    Return 0 if there is no such transformation sequence.
    All words have the same length.
    All words contain only lowercase alphabetic characters.
    You may assume no duplicates in the word list.
    You may assume beginWord and endWord are non-empty and are not the same.

```
Example 1:

Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.

Example 2:

Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: 0
```

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.

题目大意：给一个单词集合，一个开始单词和结尾单词，找到一个最短的梯子，将两个单词串起来，梯子中的每个单词都属于单词集合，梯子中每两个相邻单词之间只差一个字母。

解题思路：用BFS，搜索单词集合中所有可用的单词，记录完成搜索所需要的层数。

{% highlight c++ linenos %}
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        //convert word list into set, assume no duplicates exists
        unordered_set<string> dict(wordList.begin(), wordList.end());
        //check if the second word can be sucessor of first word
        auto isSuccessor = [](const string &first, const string &second){
            if(first.size() != second.size()) return false;
            int diff = 0;
            for(int i = 0; i < first.size(); i++){
                if(first[i] != second[i])
                    diff++;
            }
            return diff == 1;
        };

        //queue for bfs
        queue<string> Q;
        Q.push(beginWord);
        int steps = 1;
        while(!Q.empty()){
            int num = Q.size();
            //traversal all words of current level
            while(num-- > 0){
                string word = Q.front();
                Q.pop();
                if(word == endWord){
                    return steps;
                }
                //search for word of next level
                for(auto it = dict.begin(); it != dict.end();){
                    if(isSuccessor(word, *it)){
                        Q.push(*it);
                        it = dict.erase(it);
                    }else{
                        ++it;
                    }
                }
            }
            steps++;
        }

        return 0; //if it's impossible to find a ladder
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 464 ms, faster than 39.00% of C++ online submissions for Word Ladder.
Memory Usage: 13.2 MB, less than 66.67% of C++ online submissions for Word Ladder.
```

# 130. Surrounded Regions

Medium

Given a 2D board containing 'X' and 'O' (the letter O), capture all regions surrounded by 'X'.

A region is captured by flipping all 'O's into 'X's in that surrounded region.

```
Example:

X X X X
X O O X
X X O X
X O X X

After running your function, the board should be:

X X X X
X X X X
X X X X
X O X X
```

Explanation:

Surrounded regions shouldn’t be on the border, which means that any 'O' on the border of the board are not flipped to 'X'. Any 'O' that is not on the border and it is not connected to an 'O' on the border will be flipped to 'X'. Two cells are connected if they are adjacent cells connected horizontally or vertically.

题目大意：和下围棋一样，将被‘X’包围的'O'都翻转成‘X’。

解题思路：用BFS，先找到边界上没有被包围的‘O’，搜索出邻接的‘O’，标记出来。将没有被搜索出来的'O'全部翻转。

{% highlight c++ linenos %}
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        int m = board.size();
        if(m == 0){
            return;
        }
        int n = board[0].size();
        queue<pair<int, int>> Q;
        //save the boundary element
        for(int i = 0; i < m; i++){
            if(board[i][0] == 'O'){
                board[i][0] = '*';
                Q.push(pair<int, int>(i, 0));
            }

            if(board[i][n-1] == 'O'){
                board[i][n-1] = '*';
                Q.push(pair<int, int>(i, n-1));
            }        
        }

        for(int j = 1; j < n; j++){
            if(board[0][j] == 'O'){
                board[0][j] = '*';
                Q.push(pair<int, int>(0, j));
            }

            if(board[m-1][j] == 'O'){
                board[m-1][j] = '*';
                Q.push(pair<int, int>(m-1, j));
            }
        }

        //search the four direction
        pair<int, int> offset[4] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };
        while(!Q.empty()){
            int num = Q.size();
            while(num-- > 0){
                pair<int, int> pos = Q.front();
                Q.pop();
                for(int i = 0; i < 4; i++){
                    int x = pos.first + offset[i].first,
                        y = pos.second + offset[i].second;
                    if(x < 0 || x >= m || y < 0 || y >= n)
                        continue;
                    if(board[x][y] == 'O'){
                        board[x][y] = '*';
                        Q.push(pair<int, int>(x, y));
                    }
                }
            }
        }

        //filp the cell
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(board[i][j] == 'O'){
                    board[i][j] = 'X';
                }
                if(board[i][j] == '*'){
                    board[i][j] = 'O';
                }
            }
        }

        return;        
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 32 ms, faster than 80.98% of C++ online submissions for Surrounded Regions.
Memory Usage: 14.2 MB, less than 67.53% of C++ online submissions for Surrounded Regions.
```

# 200. Number of Islands

Medium

Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

```
Example 1:

Input:
11110
11010
11000
00000

Output: 1

Example 2:

Input:
11000
11000
00100
00011

Output: 3
```

题目大意：矩阵中‘0’为海洋，‘1’为陆地，相连的陆地组成岛屿，要求求出岛屿的个数。

解题思路：用BFS，测出和标记岛屿的个数，一个岛屿的所有陆地可以在一次BFS中得到。

{% highlight c++ linenos %}
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size();
        if(m == 0){
            return 0;
        }
        int n = grid[0].size();

        //plot grid with the index of island
        const pair<int, int> offset[4] = { {-1, 0}, {1, 0}, {0, -1}, {0, 1} };
        auto plotGrid = [&](int row, int col, int steps){
            queue<pair<int, int>> Q;
            Q.push(pair<int, int>(row, col));
            while(!Q.empty()){
                int num = Q.size();
                while(num-- > 0){
                    pair<int, int> pos = Q.front();
                    Q.pop();
                    for(int i = 0; i < 4; i++){
                        int x = pos.first + offset[i].first,
                            y = pos.second + offset[i].second;
                        if(x < 0 || x >= m || y < 0 || y >= n)
                            continue;

                        if(grid[x][y] != '*')
                            continue;

                        grid[x][y] = steps + '1';
                        Q.push(pair<int, int>(x, y));
                    }
                }
            }
        };

        //mark all the land
        for(auto & row : grid){
            for(auto & cell : row){
                if(cell == '1'){
                    cell = '*';
                }
            }
        }

        int steps = 0;
        //keep search until no more land is remain un-markded
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                if(grid[i][j] != '*')
                    continue;

                //mark all lands of one island
                plotGrid(i, j, steps);
                steps++;
            }
        }

        return steps;       
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 16 ms, faster than 84.06% of C++ online submissions for Number of Islands.
Memory Usage: 11.4 MB, less than 24.75% of C++ online submissions for Number of Islands.
```

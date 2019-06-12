---
layout: post
title: LeetCode专题-深度优先搜索
---

## 目录
425. Word Squares
39. Combination Sum
40. Combination Sum II

## 425. Word Squares

Given a set of words (without duplicates), find all word squares you can build from them.

A sequence of words forms a valid word square if the kth row and column read the exact same string, where 0 ≤ k < max(numRows, numColumns).

For example, the word sequence ["ball","area","lead","lady"] forms a word square because each word reads the same both horizontally and vertically.

b a l l
a r e a
l e a d
l a d y

Note:

    There are at least 1 and at most 1000 words.
    All words will have the exact same length.
    Word length is at least 1 and at most 5.
    Each word contains only lowercase English alphabet a-z.

 ```
Example 1:

Input:
["area","lead","wall","lady","ball"]

Output:
[
  [ "wall",
    "area",
    "lead",
    "lady"
  ],
  [ "ball",
    "area",
    "lead",
    "lady"
  ]
]
```
Explanation:
The output consists of two word squares. The order of output does not matter (just the order of words in each word square matters).

题目大意：给定一组无重复的单词，要求选出几个单词，组成一个单词square，横着读和纵着读的内容相同。

解题思路：用前缀表达式记录拥有特定前缀的单词，再用深度优先搜索在满足前缀条件的单词中进行搜索。

```c++
//(DFS, backtrace + Prefix tree)
struct TrieNode {
	TrieNode(){}
	~TrieNode() {}
	shared_ptr<TrieNode>	next[26];
	vector<string>			words; //words in dict that have the prefix
	// for this node
};
void insertWS(const string &word, shared_ptr<TrieNode> node) {
	for (auto const c : word) {
		int idx = c - 'a'; //get idx of node for c
		if (node->next[idx] == nullptr) {
			//create new trie node
			node->next[idx] = make_shared<TrieNode>();
		}
		// move to prefix tree(trie) node with c
		node = node->next[idx];
		// word is one with prefix contained in node
		node->words.push_back(word);
	}
}
vector<string> findCandidates(shared_ptr<TrieNode> node, const string &prefix) {
	for (auto const & c : prefix) {
		int idx = c - 'a';
		if (node->next[idx] == nullptr) {
			return {};
		}
		node = node->next[idx]; //level-by-level, search the node for input prefix
	}
	return node->words; //return a copy of words with prefix
}
void wordSquaresDFS(shared_ptr<TrieNode> node, const int N, vector<string> &path, vector<vector<string>> &ans) {
	size_t newIdx = path.size();
	if ((int)newIdx == N) {
		//if path is long enough, save into ans
		ans.push_back(path);
		return;
	}

	string prefix;
	//path length must be shorter than word length
	for (auto const & word : path) {
		//get the newIdx-th column
		prefix += word[newIdx];
	}

	//take the newIdx-th colum as the new row, find all candidates
	//	words that contain the row as prefix
	vector<string> candidates = findCandidates(node, prefix);
	for (auto const & candidate : candidates) {
		//choose and un-choose one candidate
		path.push_back(candidate);
		wordSquaresDFS(node, N, path, ans); //advance to next level
		path.pop_back(); //un-choose before return
	}
}
vector<vector<string>> wordSquares(vector<string>& words) {
	vector<vector<string>> ans;
	if (words.size() == 0) {
		return ans;
	}

	// build the trie tree
	const int N = (int)words.at(0).size();
	auto root = make_shared<TrieNode>();
	for (auto const & word : words) {
		insertWS(word, root);
	}

	// DFS
	vector<string> path;
	for (auto const & word : words) {
		path.push_back(word);
		wordSquaresDFS(root, N, path, ans);
		path.pop_back();
	}

	return move(ans);
}

void WordSquares() {
	cout << "WordSquares" << endl;
	auto anss = wordSquares(vector<string>{ "area","lead","wall","lady","ball" });
	for (auto & ans : anss) {
		Print(ans);
	}
}
```

## 39. Combination Sum

Medium

Given a set of candidate numbers (candidates) (without duplicates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

The same repeated number may be chosen from candidates unlimited number of times.

Note:

    All numbers (including target) will be positive integers.
    The solution set must not contain duplicate combinations.

```
Example 1:

Input: candidates = [2,3,6,7], target = 7,
A solution set is:
[
  [7],
  [2,2,3]
]

Example 2:

Input: candidates = [2,3,5], target = 8,
A solution set is:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```
题目大意：给定一个数组，要从数组中选几个数，使之和等于一个目标值，数组中每个数都可以被选任意多次。

解题思路：利用深度优先搜索，对数组中的每个数一一“选中”，测试哪一个分支满足条件，保留满足条件的分支。

```c++
class Solution {
    void combinationSumDFS(const vector<int>& candidates, 		vector<int>& path, int target, int start, vector<vector<int>> &ans) {
        if (target < 0) return;
        if (target == 0) {ans.push_back(path); return;}
        for (int i = start; i < candidates.size(); ++i) {
            path.push_back(candidates[i]);
            combinationSumDFS(candidates, path, target - candidates[i], i, ans);
            path.pop_back();
        }
    }
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> path;
        combinationSumDFS(candidates, path, target, 0, ans);
        return move(ans);
    }
};
```
测试一下，
```
Success
Details
Runtime: 8 ms, faster than 99.33% of C++ online submissions for Combination Sum.
Memory Usage: 9.5 MB, less than 80.12% of C++ online submissions for Combination Sum.
```

## 40. Combination Sum II
Medium

Given a collection of candidate numbers (candidates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

Each number in candidates may only be used once in the combination.

Note:

    All numbers (including target) will be positive integers.
    The solution set must not contain duplicate combinations.

```
Example 1:

Input: candidates = [10,1,2,7,6,1,5], target = 8,
A solution set is:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]

Example 2:

Input: candidates = [2,5,2,1,2], target = 5,
A solution set is:
[
  [1,2,2],
  [5]
]
```
题目大意：和上一题基本相同，有差别的在于，数组中的每个元素都只能被选中一次。

解题思路：加一个记录当前数组最近“选中”位置的变量。

```c++
class Solution {
    void combinationSumDFS(const vector<int>& candidates, vector<int>& path, int target, int start, vector<vector<int>> &ans) {
        if (target == 0) {
            sort(path.begin(), path.end());
            if (find(ans.begin(), ans.end(), path) == ans.end()) {
                ans.push_back(path);
            }
            return;
        }
        else if (target < 0) {
            return;
        }

        // DFS search range
        for (int i = start; i < (int)candidates.size(); i++) {
            path.push_back(candidates[i]);
            combinationSumDFS(candidates, path, target - candidates[i], i + 1, ans);
            path.pop_back();
        }
    }    
public:
    
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());
        vector<vector<int>> ans;
        vector<int> path;
        combinationSumDFS(candidates, path, target, 0, ans);
        return move(ans);        
    }
};
```
测试一下，
```
Success
Details
Runtime: 44 ms, faster than 15.78% of C++ online submissions for Combination Sum II.
Memory Usage: 9 MB, less than 81.12% of C++ online submissions for Combination Sum II.
```
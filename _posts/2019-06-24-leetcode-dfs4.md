---
layout: post
title: LeetCode专题-深度优先搜索(四)
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 140. Word Break II

Hard

Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, add spaces in s to construct a sentence where each word is a valid dictionary word. Return all such possible sentences.

Note:

    The same word in the dictionary may be reused multiple times in the segmentation.
    You may assume the dictionary does not contain duplicate words.

```
Example 1:

Input:
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
Output:
[
  "cats and dog",
  "cat sand dog"
]

Example 2:

Input:
s = "pineapplepenapple"
wordDict = ["apple", "pen", "applepen", "pine", "pineapple"]
Output:
[
  "pine apple pen apple",
  "pineapple pen apple",
  "pine applepen apple"
]
Explanation: Note that you are allowed to reuse a dictionary word.

Example 3:

Input:
s = "catsandog"
wordDict = ["cats", "dog", "sand", "and", "cat"]
Output:
[]
```

题目大意：上接139题，给一个字符串和一个字符串字典，要求利用字典中的元素分割字符串，找到所有的分割方法。

解题思路：看起来用DFS就可以解决，但是单纯的DFS不能通过一些测试用例。

{% highlight c++ linenos %}
class Solution {
public:
    void WordBreakDFS(const string &s, const set<string> &dict, int start, vector<string> &path, vector<string> &ans){
        auto makePathStr = [](const vector<string> &path) -> string{
            if(path.size() == 0) return "";
            string pathStr = path[0];
            for(int i = 1; i < path.size(); i++) {
                pathStr += " " + path[i];
            }
            return pathStr;
        };

        if(start == s.size()){
            ans.push_back(makePathStr(path));
            return;
        }

        for(int i = start; i < s.size(); i++){
            auto word = s.substr(start, i - start + 1);
            if(dict.count(word)){
                path.push_back(word);
                WordBreakDFS(s, dict, i + 1, path, ans);
                path.pop_back();
            }
        }
    }

    vector<string> wordBreak(string s, vector<string>& wordDict) {
        vector<string> ans, path;
        set<string> dict(wordDict.begin(), wordDict.end());
        WordBreakDFS(s, dict, 0, path, ans);
        return move(ans);
    }
};
{% endhighlight %}
重复搜索太多，导致测试用例超时
```
Time Limit Exceeded
Details
Last executed input
"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaabaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
["a","aa","aaa","aaaa","aaaaa","aaaaaa","aaaaaaa","aaaaaaaa","aa....
```
在花花酱的博客中学到了一种[方法](https://zxi.mytechroad.com/blog/leetcode/leetcode-140-word-break-ii/)，

解题思路：利用记忆递归，记忆字符串s的分割集合subset。在字符串s中寻找分割点，分为左右两个部分，左半字符串在字典中，递归分割右半字符串。

{% highlight c++ linenos %}
class Solution {
public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> dict(wordDict.cbegin(), wordDict.cend());
        return wordBreak(s, dict);
    }
private:
    // >> append({"cats and", "cat sand"}, "dog");
    // {"cats and dog", "cat sand dog"}
    vector<string> append(const vector<string>& prefixes, const string& word) {
        vector<string> results;
        for(const auto& prefix : prefixes)
            results.push_back(prefix + " " + word);
        return move(results);
    }
    
    const vector<string>& wordBreak(string s, unordered_set<string>& dict) {
        // Already in memory, return directly
        if(mem_.count(s)) return mem_[s];
        
        // Answer for s
        vector<string> ans;
        
        // s in dict, add it to the answer array
        if(dict.count(s)) 
            ans.push_back(s);
        
        for(int j=1;j<s.length();++j) {
            // Check whether right part is a word
            const string& right = s.substr(j);
            if (!dict.count(right)) continue;
            
            // Get the ans for left part
            const string& left = s.substr(0, j);
            const vector<string> left_ans = 
                append(wordBreak(left, dict), right);
            
            // Notice, can not use mem_ here,
            // since we haven't got the ans for s yet.
            ans.insert(ans.end(), left_ans.begin(), left_ans.end());
        }
        
        // memorize and return
        mem_[s].swap(ans);
        return mem_[s];
    }
private:
    unordered_map<string, vector<string>> mem_;
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 16 ms, faster than 78.45% of C++ online submissions for Word Break II.
Memory Usage: 15 MB, less than 46.53% of C++ online submissions for Word Break II.
```
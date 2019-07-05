---
layout: post
title: LeetCode专题 - 排列
tags: [Leetcode, C/C++, SQL]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 46. Permutations

Medium

Given a collection of distinct integers, return all possible permutations.

```
Example:

Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```
题目大意：给一组无重复的整数，要求出用它们进行组合可以形成的所有排列。

解题思路：迭代，每次从当前排列出发，计算出最近的一个排列，直到所有排列都被列举出来。

{% highlight c++ linenos %}
class Solution {
public:
    bool calcPremute(vector<int>& nums, int n){
        int i = 0;
        //find the last element in ascending order
        for(i = n - 2; i >= 0 && nums[i] >= nums[i+1]; i--){}
        if(i < 0){
            return false; //all permutations have been found
        }

        int k = 0;
        //find the nearest element nums[k] that locate at right of nums[i]
        //  and greater than it.
        for(k = n - 1; k > i && nums[k] <= nums[i]; k--){}
        swap(nums[i], nums[k]); //exchange k, i
        //revers the last bits
        reverse(nums.begin() + i + 1, nums.begin() + n);
        return true;
    }

    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        sort(nums.begin(), nums.end());
        do{
            ans.push_back(nums);
        }while(calcPremute(nums, nums.size()));
        return move(ans);
    }        
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 12 ms, faster than 80.92% of C++ online submissions for Permutations.
Memory Usage: 9 MB, less than 94.01% of C++ online submissions for Permutations.
```

# 47. Permutations II

Medium

Given a collection of numbers that might contain duplicates, return all possible unique permutations.

```
Example:

Input: [1,1,2]
Output:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

题目大意：和上题不同的是，数组中可能存在重复的元素。

解题思路：过滤掉重复的排列。

{% highlight c++ linenos %}
class Solution {
public:
    bool calcPremute(vector<int>& nums, int n){
        int i = 0;
        for(i = n - 2; i >= 0 && nums[i] >= nums[i+1]; i--){}
        if(i < 0){
            return false;
        }

        int k = 0;
        for(k = n - 1; k > i && nums[k] <= nums[i]; k--){}
        swap(nums[i], nums[k]);
        reverse(nums.begin() + i + 1, nums.begin() + n);
        return true;
    }
    
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> ans;
        sort(nums.begin(), nums.end());
        do{
            if(find(ans.begin(), ans.end(), nums) == ans.end()){
                ans.push_back(nums);
            }
        }while(calcPremute(nums, nums.size()));
        return move(ans);
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 44 ms, faster than 17.78% of C++ online submissions for Permutations II.
Memory Usage: 9.7 MB, less than 95.88% of C++ online submissions for Permutations II.
```
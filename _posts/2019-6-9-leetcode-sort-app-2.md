---
layout: post
title: LeetCode专题-排序应用(二)
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 34. Find First and Last Position of Element in Sorted Array

Medium

Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.

Your algorithm's runtime complexity must be in the order of O(log n).

If the target is not found in the array, return [-1, -1].

```
Example 1:

Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]

Example 2:

Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

题目大意：给一个排序好的数组和一个搜索目标值，要求找出一个范围，这个范围内的数组值等于目标值。

解题思路：二分搜索，可以利用cpp标准库中的lower_bound()和upper_bound()。需要注意的是范围不存在时的处理。

```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans(2, -1);
        if (nums.size() > 0 ) {
            int low = lower_bound(nums.begin(), nums.end(), target) - nums.begin(); //equal or greater
            int high = upper_bound(nums.begin(), nums.end(), target) - nums.begin() - 1; //greater
            if(low <= high){
                ans[0] = low;
                ans[1] = high;
            }
        }

        return move(ans);        
    }
};
```
测试一下，
```
Success
Details
Runtime: 8 ms, faster than 95.21% of C++ online submissions for Find First and Last Position of Element in Sorted Array.
Memory Usage: 10.4 MB, less than 53.82% of C++ online submissions for Find First and Last Position of Element in Sorted Array.
```

# 1. Two Sum

Easy

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

```
Example:

Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

题目大意：在一个数组中，找到两个数，两数之和等于目标值。

解题思路：将数组进行排序，从两头找起，逐步缩小两数和和目标值的差距。稍微麻烦的是，题目要求返回数在数组中的索引，特别是找到两个相等的目标数后，求索引要留意。

{% highlight c++ linenos %}
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        auto nums_cp = nums;;
        sort(nums.begin(), nums.end());
        auto beg = nums.begin(), end = nums.end()-1;
        while(beg < end){
            int sum = *beg + *end;
            if(sum == target){
                vector<int>::iterator first, second;
                if(*beg == *end){
                    //second num must comes after first if their value is equal
                    first = find(nums_cp.begin(), nums_cp.end(), *beg);
                    second = find(first + 1, nums_cp.end(), *end);           
                }else{
                    first = find(nums_cp.begin(), nums_cp.end(), *beg);
                    second = find(nums_cp.begin(), nums_cp.end(), *end);           
                }

                return vector<int>{first - nums_cp.begin(), second - nums_cp.begin()};
            }else if (sum < target) {
                beg++;
            }else{
                end--;
            }
        }

        return vector<int>{-1, -1};        
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 8 ms, faster than 96.73% of C++ online submissions for Two Sum.
Memory Usage: 9.4 MB, less than 62.78% of C++ online submissions for Two Sum.
```
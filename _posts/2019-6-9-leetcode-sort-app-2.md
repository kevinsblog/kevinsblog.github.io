---
layout: post
title: LeetCode专题-排序应用(二)
---

## 34. Find First and Last Position of Element in Sorted Array

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
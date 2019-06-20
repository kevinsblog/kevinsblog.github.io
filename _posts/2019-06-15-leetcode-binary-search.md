---
layout: post
title: LeetCode专题-二分搜索应用
tags: [Leetcode]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

<span id="jump81">Medium</span>

# 81. Search in Rotated Sorted Array II

Medium

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., [0,0,1,2,2,5,6] might become [2,5,6,0,0,1,2]).

You are given a target value to search. If found in the array return true, otherwise return false.
```
Example 1:

Input: nums = [2,5,6,0,0,1,2], target = 0
Output: true

Example 2:

Input: nums = [2,5,6,0,0,1,2], target = 3
Output: false
```
Follow up:

    This is a follow up problem to Search in Rotated Sorted Array, where nums may contain duplicates.
    Would this affect the run-time complexity? How and why?

题目大意：一个被翻折的升序数组，要求搜索一个元素是否存在。

解题思路：用二分搜索，但是需要稍作变形。如图，由于数组被翻折，目标值可能落在这两个区间，
![两种情形](https://img-blog.csdn.net/20160607112155596?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
我们的思路是，首先判断数组的哪一半是升序，未被翻折的，然后判断目标值是否在这个未翻折的区间，若是，则下一轮搜索在这一区间进行，否则，在另一区间进行。当无法判断哪一个区间是完好的时，只能一个一个元素地挪动区间边界。

```c++
class Solution {
public:
    bool search(vector<int>& nums, int target) {
        if(nums.size() == 0) //impossible to find target in empty array
            return false;
        
        auto beg = nums.begin(), end = nums.end() - 1;
        while(beg <= end){
            auto mid = beg + (end - beg)/2;
            if(*mid == target || *beg == target || *end == target){
                return true;
            }

            if(*mid == *end){ 
                end--; //cannot check which half is ascending
            }else if(*mid > *end){ //first half is ascending
                if(*beg < target && target < *mid){
                    end = mid - 1; //target fulls in first half
                } else {
                    beg = mid + 1;
                }
            }else{ //second half is ascending
                if(*mid < target && target < *end){
                    beg = mid + 1; //target fulls in second half
                } else {
                    end = mid - 1;
                }
            }
        }

        return false;        
    }
};
```
测试一下，
```
Success
Details
Runtime: 4 ms, faster than 99.77% of C++ online submissions for Search in Rotated Sorted Array II.
Memory Usage: 8.8 MB, less than 20.65% of C++ online submissions for Search in Rotated Sorted Array II.
```
---
layout: post
title: LeetCode专题-二叉索引树(Binary Index Tree/Fenwick Tree)
---

## Fenwick Tree

> A Fenwick tree or binary indexed tree is a data structure that can efficiently update elements and calculate prefix sums in a table of numbers. This structure was proposed by Peter Fenwick in 1994 to improve the efficiency of arithmetic coding compression algorithms.

二叉搜索树(BIT)可以用于维护和查询区间信息，它可以在O(logn)的时间复杂度下求出区间和。

[在线示例](https://visualgo.net/zh/fenwicktree)

[一棵Fenwick树]!(https://upload.wikimedia.org/wikipedia/commons/thumb/d/dc/BITDemo.gif/300px-BITDemo.gif)

在建立和更新数组时，从左向右，从叶子节点向根节点依次求和。
在查询区间和时，从右向左，求出前缀和的差值。

下面是BIT的C和C++实现。
```c
#define LSB(i) ((i) & -(i)) // zeroes all the bits except the least significant one

int A[SIZE];

int sum(int i) // Returns the sum from index 1 to i
{
    int sum = 0;
    while (i > 0) 
        sum += A[i], i -= LSB(i);
    return sum;
}
 
void add(int i, int k) // Adds k to element with index i
{
    while (i < SIZE) 
        A[i] += k, i += LSB(i);
}
```

```c++
class fenwickTree{
    vector<int>     sums; //parital sum
    static inline int lowbit(int x) {return x & (-x);}
public:
    //index start from 1
    fenwickTree(int n):sums(n+1, 0){}

    void update(int i, int delta){
        while(i < sums.size()){
            sums[i] += delta;
            i += lowbit(i); //remove the lowest bit 1
        }
    }

    int query(int i) const {
        int sum = 0;
        while(i > 0){
            sum += sums[i];
            i -= lowbit(i);
        }
        return sum;
    }
};
```

## 307. Range Sum Query - Mutable

Medium

Given an integer array nums, find the sum of the elements between indices i and j (i ≤ j), inclusive.

The update(i, val) function modifies nums by updating the element at index i to val.

```
Example:

Given nums = [1, 3, 5]

sumRange(0, 2) -> 9
update(1, 2)
sumRange(0, 2) -> 8
```

Note:

    The array is only modifiable by the update function.
    You may assume the number of calls to update and sumRange function is distributed evenly.


```c++
class NumArray {
    fenwickTree  ft;
    vector<int>  nums_;
public:
    NumArray(vector<int>& nums):ft(nums.size() + 1),nums_(nums.size(), 0) {
        for(int i = 0; i < nums.size(); i++){
            ft.update(i+1, nums[i]);
            nums_[i] = nums[i];
        }
    }
    
    void update(int i, int val) {
        ft.update(i + 1, val - nums_[i]);
        nums_[i] = val;
    }
    
    int sumRange(int i, int j) {
        return ft.query(j+1) - ft.query(i);
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray* obj = new NumArray(nums);
 * obj->update(i,val);
 * int param_2 = obj->sumRange(i,j);
 */
```
测试一下
```
Success
Details
Runtime: 36 ms, faster than 92.57% of C++ online submissions for Range Sum Query - Mutable.
Memory Usage: 19.3 MB, less than 39.95% of C++ online submissions for Range Sum Query - Mutable.
```
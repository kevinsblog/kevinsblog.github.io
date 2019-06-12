---
layout: post
title: LeetCode专题-数组和矩阵
---

## 目录

48. Rotate Image
218. The Skyline Problem
56. Merge Intervals

## 48. Rotate Image

Medium

You are given an n x n 2D matrix representing an image.

Rotate the image by 90 degrees (clockwise).

Note:

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.

```
Example 1:

Given input matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

rotate the input matrix in-place such that it becomes:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]

Example 2:

Given input matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 

rotate the input matrix in-place such that it becomes:
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```
题目大意：将输入矩阵顺时针旋转90度。

解题思路：先将矩阵按行反序，然后交换(x,y)和(y,x)处的元素。

```c++
class Solution {
public:
    /*
    1  2  3                 7  8  9          7  *4  *1

    4  5  6      -->        4  5  6　　 -->  *8  5  *2　　

    7  8  9                 1  2  3　　　　　 *9  *6  3
    reverse by row
    exchange (i, j) with (j, i)
    */
    void rotate(vector<vector<int>>& matrix) {
        reverse(matrix.begin(), matrix.end());
        int rows = (int)matrix.size();
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < i; j++) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }
    }
};
```
测试一下，
```
Success
Details
Runtime: 4 ms, faster than 94.67% of C++ online submissions for Rotate Image.
Memory Usage: 9 MB, less than 71.33% of C++ online submissions for Rotate Image.
```

## 218. The Skyline Problem

Hard

A city's skyline is the outer contour of the silhouette formed by all the buildings in that city when viewed from a distance. Now suppose you are given the locations and height of all the buildings as shown on a cityscape photo (Figure A), write a program to output the skyline formed by these buildings collectively (Figure B).
Buildings Skyline Contour
[A]!(https://assets.leetcode.com/uploads/2018/10/22/skyline1.png)
[B]!(https://assets.leetcode.com/uploads/2018/10/22/skyline2.png)

The geometric information of each building is represented by a triplet of integers [Li, Ri, Hi], where Li and Ri are the x coordinates of the left and right edge of the ith building, respectively, and Hi is its height. It is guaranteed that 0 ≤ Li, Ri ≤ INT_MAX, 0 < Hi ≤ INT_MAX, and Ri - Li > 0. You may assume all buildings are perfect rectangles grounded on an absolutely flat surface at height 0.

For instance, the dimensions of all buildings in Figure A are recorded as: [ [2 9 10], [3 7 15], [5 12 12], [15 20 10], [19 24 8] ] .

The output is a list of "key points" (red dots in Figure B) in the format of [ [x1,y1], [x2, y2], [x3, y3], ... ] that uniquely defines a skyline. A key point is the left endpoint of a horizontal line segment. Note that the last key point, where the rightmost building ends, is merely used to mark the termination of the skyline, and always has zero height. Also, the ground in between any two adjacent buildings should be considered part of the skyline contour.

For instance, the skyline in Figure B should be represented as:[ [2 10], [3 15], [7 12], [12 0], [15 10], [20 8], [24, 0] ].

Notes:

    The number of buildings in any input list is guaranteed to be in the range [0, 10000].
    The input list is already sorted in ascending order by the left x position Li.
    The output list must be sorted by the x position.
    There must be no consecutive horizontal lines of equal height in the output skyline. For instance, [...[2 3], [4 5], [7 5], [11 5], [12 7]...] is not acceptable; the three lines of height 5 should be merged into one in the final output as such: [...[2 3], [4 5], [12 7], ...]

题目大意：按照[start, end, height]给定一个城市中每个建筑的轮廓，要求求出这个城市的天际线，并以[start, height]标出所有水平线的开端点。

解题思路：扫描数组，但是当用例中存在极端宽的建筑时，内存肯定是不够的

```c++
class Solution {
public:
    vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
       long width = 0, low = 0, high = 0;
        for(auto & building : buildings){
            low = min(low, (long)building[0]);
            high = max(high, (long)building[1]);
        }
        width = high - low + 1;
        vector<long> skyLine(width, 0);
        for(auto & building : buildings){
            /*replace_if(skyLine.begin() + building[0], skyLine.begin() + building[1], 
                bind2nd(greater<int>(), width), width);*/
            for(long i = building[0]; i < building[1]; i++){
                skyLine[i] = max((long)building[2],skyLine[i]);
            }
        }

        vector<vector<int>> ans;
        for(int i = 1; i < skyLine.size(); i++){
            if(i == skyLine.size()-1 || skyLine[i] != skyLine[i-1]){
                ans.push_back(vector<int>{i + (int)low, (int)skyLine[i]});
            }
        }
        return move(ans);             
    }
};
```
测试一下，
```
Your input
[[2,9,10],[3,7,15],[5,12,12],[15,20,10],[19,24,8]]
0 | 10 | 15 | 15 | 15 | 15 | 12 | 12 | 12 | 12 | 12 | 0 | 0 | 0 | 10 | 10 | 10 | 10 | 10 | 8 | 8 | 8 | 8 | 0 | 
Output
[[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]
Expected
[[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]

Last executed input (Runtime error)
[[0,2147483647,2147483647]]
```

## 56. Merge Intervals

Medium

Given a collection of intervals, merge all overlapping intervals.

```
Example 1:

Input: [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].

Example 2:

Input: [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.

NOTE: input types have been changed on April 15, 2019. Please reset to default code definition to get new method signature.
```

题目大意：给一系列区间对，合并其中相交的区间，输出合成的大区间。

解题思路：先将区间进行排序，然后遍历区间进行合并。

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        if(intervals.size() < 2){ //no need to do merge
            return intervals;
        }
        //sort intervals in ascending order
        sort(intervals.begin(), intervals.end(), [](vector<int> &lhs, vector<int> &rhs){
            return lhs[0] < rhs[0] || (lhs[0] == rhs[0] && lhs[1] < rhs[1]);
        });
        vector<vector<int>> ans;
        //[start, end] is the interval to be merge
        int start = intervals[0][0], end = intervals[0][1];
        for(int i = 1; i < intervals.size(); i++){
            if(intervals[i][0] <= end){
                //interval i can be merged
                end = max(intervals[i][1], end);
            }else{
                //cannot merge, make new interval
                ans.push_back(vector<int>{start, end});
                start = intervals[i][0];
                end = max(intervals[i][1], end);
            }
        }
        ans.push_back(vector<int>{start, end});
        return move(ans);        
    }
};
```
测试一下
```
Success
Details
Runtime: 16 ms, faster than 78.11% of C++ online submissions for Merge Intervals.
Memory Usage: 12.6 MB, less than 13.42% of C++ online submissions for Merge Intervals.
```
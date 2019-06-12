---
layout: post
title: LeetCode专题-排序应用
---

#853. Car Fleet
Medium

N cars are going to the same destination along a one lane road.  The destination is target miles away.

Each car i has a constant speed speed[i] (in miles per hour), and initial position position[i] miles towards the target along the road.

A car can never pass another car ahead of it, but it can catch up to it, and drive bumper to bumper at the same speed.

The distance between these two cars is ignored - they are assumed to have the same position.

A car fleet is some non-empty set of cars driving at the same position and same speed.  Note that a single car is also a car fleet.

If a car catches up to a car fleet right at the destination point, it will still be considered as one car fleet.

How many car fleets will arrive at the destination?

题目大意：高速公路上有一系列车从不同的位置出发，它们各自的速度不同，从同一个方向前往同一个终点，它们不能超车，一旦遇上只能前后排成一个car fleet，问到达终点时有多少个car fleet。

解题思路：计算每辆车到终点需要的时间，时间长的一定会被时间短的挡住，成为一个car fleet。下面尝试用python解题。

```python3
class Solution:
    def carFleet(self, target: int, pos: List[int], speed: List[int]) -> int:
        #corner case handle
        if len(pos) != len(speed) or len(pos) == 0 :
            return 0

        cars = []
        #car position, time to reach target
        for i in range(len(pos)):
            cars.append((pos[i], 1.0*(target - pos[i])/speed[i]))

        #sort in reverse order
        cars.sort(key=lambda tup: tup[0], reverse=True)
        car_fleet = 0
        max_time = 0
        for _, t in cars:
            #max_time record the time for prev car fleet, if the next car
            # need less time to reach target, it would be blocked and join
            # the prev car fleet
            if t > max_time:
                #new car fleet exist if need to take event more to reach
                #   the final target
                max_time = t
                car_fleet += 1
        return car_fleet
        
```
在线测试一下
```
Success
[Details]
Runtime: 76 ms, faster than 93.20% of Python3 online submissions for Car Fleet.
Memory Usage: 15.3 MB, less than 59.78% of Python3 online submissions for Car Fleet.
```

#976. Largest Perimeter Triangle
Easy

Given an array A of positive lengths, return the largest perimeter of a triangle with non-zero area, formed from 3 of these lengths.

If it is impossible to form any triangle of non-zero area, return 0.

 

Example 1:

Input: [2,1,2]
Output: 5

Example 2:

Input: [1,2,1]
Output: 0

Example 3:

Input: [3,2,3,4]
Output: 10

Example 4:

Input: [3,6,2,3]
Output: 8

Note:

    3 <= A.length <= 10000
    1 <= A[i] <= 10^6

题目大意：给一组数，找出三个数组成三角形，要求其周长最长。

解题思路：要组成三角形，要求两短边之和大于第三边，除此之外，选的三边越长，周长也越长。

```python3
class Solution:
    def largestPerimeter(self, nums: List[int]) -> int:
        nums.sort(reverse=True) #sort in reverse order
        for i in range(len(nums) - 2):
            if nums[i] < nums[i+1] + nums[i+2]:
                return nums[i] + nums[i+1] + nums[i+2]
        return 0        
```
测试一下，
```
Success
[Details]
Runtime: 72 ms, faster than 90.14% of Python3 online submissions for Largest Perimeter Triangle.
Memory Usage: 13.9 MB, less than 62.16% of Python3 online submissions for Largest Perimeter Triangle.
```

#945. Minimum Increment to Make Array Unique
Medium

Given an array of integers A, a move consists of choosing any A[i], and incrementing it by 1.

Return the least number of moves to make every value in A unique.

Example 1:

Input: [1,2,2]
Output: 1
Explanation:  After 1 move, the array could be [1, 2, 3].

Example 2:

Input: [3,2,1,2,1,7]
Output: 6
Explanation:  After 6 moves, the array could be [3, 4, 1, 2, 5, 7].
It can be shown with 5 or less moves that it is impossible for the array to have all unique values.

Note:
    0 <= A.length <= 40000
    0 <= A[i] < 40000

题目大意：给一组数据，数据中存在重复，要求以最小的改动，使得数据中的元素都是唯一的。

解题思路：从最小的元素开始，依次修改重复元素。

```python3
    def minIncrementForUnique(self, nums: List[int]) -> int:
        nums.sort(reverse=False)
        ans = 0
        cnt = {}
        for n in nums:
            cnt[n] = 1

        #1  1        2    2     3     7
        #0 +1(2)  +1(3)  +2(4)  +2(5) 
        for i in range(1, len(nums)):
            if nums[i] > nums[i-1]:
                continue
            #calculate distance between two adjecent num
            ans += nums[i-1] - nums[i] + 1
            #make following num one more than previous one to
            #   make it unique
            nums[i] = nums[i-1] + 1

        return ans 
```
测试一下

```
Success
[Details]
Runtime: 168 ms, faster than 42.89% of Python3 online submissions for Minimum Increment to Make Array Unique.
Memory Usage: 19 MB, less than 7.08% of Python3 online submissions for Minimum Increment to Make Array Unique.
```

#922. Sort Array By Parity II
Easy

Given an array A of non-negative integers, half of the integers in A are odd, and half of the integers are even.

Sort the array so that whenever A[i] is odd, i is odd; and whenever A[i] is even, i is even.

You may return any answer array that satisfies this condition.


Example 1:

Input: [4,2,5,7]
Output: [4,5,2,7]
Explanation: [4,7,2,5], [2,5,4,7], [2,7,4,5] would also have been accepted.


Note:

    2 <= A.length <= 20000
    A.length % 2 == 0
    0 <= A[i] <= 1000

题目大意：给定一个数组，要求调整元素顺序，使得奇数位的放的都是奇数，偶数位放的是偶数。

解题思路：对数组进行依次扫描，标记需要调整位置的元素，第二次扫描时，交换需要调整的元素。

```python3
class Solution:
    def sortArrayByParityII(self, nums: List[int]) -> List[int]:
        stat = {}
        is_even = lambda n : n%2 == 0
        is_odd = lambda n : n%2 == 1
        #mark the element with non-zero value if it needs to swap
        for i in range(len(nums)):
            if (is_even(nums[i]) and is_even(i)) or ((is_odd(nums[i])) and is_odd(i)):
                stat[i] = 0 #well aligned
            elif is_even(nums[i]):
                stat[i] = 1
            else:
                stat[i] = 2

        for i in range(len(nums)):
            if stat[i] == 0:
                continue
            else:
                #find the first valid element for swapping
                for j in range(i, len(nums)):
                    if stat[j] == 0 or stat[i] == stat[j]:
                        continue
                    nums[i], nums[j] = nums[j], nums[i]
                    stat[i] = 0 #reset stats
                    stat[j] = 0
                    break
        return nums
```
测试一下，
```
Success
[Details]
Runtime: 352 ms, faster than 5.21% of Python3 online submissions for Sort Array By Parity II.
Memory Usage: 15.9 MB, less than 5.25% of Python3 online submissions for Sort Array By Parity II.
```

#26\. Remove Duplicates from Sorted Array

Easy

Given a sorted array *nums*, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each element appear only *once* and return the new length.

Do not allocate extra space for another array, you must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

**Example 1:**

<pre>Given *nums* = **[1,1,2]**,

Your function should return length = **`2`**, with the first two elements of *`nums`* being **`1`** and **`2`** respectively.

It doesn't matter what you leave beyond the returned length.</pre>

**Example 2:**

<pre>Given *nums* = **[0,0,1,1,1,2,2,3,3,4]**,

Your function should return length = **`5`**, with the first five elements of *`nums`* being modified to **`0`**, **`1`**, **`2`**, **`3`**, and **`4`** respectively.

It doesn't matter what values are set beyond the returned length.
</pre>

题目大意：对于一个给定的数组，删除所有重复的元素。

解题思路：维持一个索引，同时将所有不重复的元素前移，覆盖重复的元素。最终的索引等于有效元素的长度。

```python3
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        if len(nums) == 0: return 0
        idx = 1
        for i in range(1, len(nums)):
            if nums[i] != nums[i - 1]:
                nums[idx] = nums[i]
                idx += 1

        return idx
```
测试一下
```
Success
[Details]
Runtime: 56 ms, faster than 95.70% of Python3 online submissions for Remove Duplicates from Sorted Array.
Memory Usage: 15 MB, less than 11.86% of Python3 online submissions for Remove Duplicates from Sorted Array.
```

#905. Sort Array By Parity
Easy

Given an array A of non-negative integers, return an array consisting of all the even elements of A, followed by all the odd elements of A.

You may return any answer array that satisfies this condition.

Example 1:

Input: [3,1,2,4]
Output: [2,4,3,1]
The outputs [4,2,3,1], [2,4,1,3], and [4,2,1,3] would also be accepted.

题目大意：对给定的数组进行划分排序，要求将偶数放到奇数之前。

解题思路：利用稳定排序，将偶数移到奇数之前。

```python3
class Solution:
    def sortArrayByParity(self, nums: List[int]) -> List[int]:
        cmp = lambda a : a%2
        nums.sort(key=cmp)
        return nums   
```
测试一下
```
Success
[Details]
Runtime: 64 ms, faster than 97.07% of Python3 online submissions for Sort Array By Parity.
Memory Usage: 13.8 MB, less than 38.36% of Python3 online submissions for Sort Array By Parity.
```

#899. Orderly Queue
Hard

A string S of lowercase letters is given.  Then, we may make any number of moves.

In each move, we choose one of the first K letters (starting from the left), remove it, and place it at the end of the string.

Return the lexicographically smallest string we could have after any number of moves.

Example 1:

Input: S = "cba", K = 1
Output: "acb"
Explanation: 
In the first move, we move the 1st character ("c") to the end, obtaining the string "bac".
In the second move, we move the 1st character ("b") to the end, obtaining the final result "acb".

题目大意：定义一次移动，将最左的K个字符放到字符串末尾。利用有限次移动，将字符串排列。

解题思路：当K>1，意味着可以利用选择排序进行完全排序，否则，尝试以每个元素为中心进行翻折。

```python3
class Solution:
    def orderlyQueue(self, s: str, k: int) -> str:
        # sort or rotation
        if k > 1:
            return ''.join(sorted(s))
        ans = s
        for i in range(len(s)):
            ans = min(ans, s[i:] + s[:i]) #try every kind of break point in rotate
        return ans   
```
测试一下
```
Success
[Details]
Runtime: 40 ms, faster than 78.70% of Python3 online submissions for Orderly Queue.
Memory Usage: 13.2 MB, less than 57.14% of Python3 online submissions for Orderly Queue.
```

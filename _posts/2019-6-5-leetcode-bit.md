---
layout: post
title: LeetCode专题-位运算
---

## 421. Maximum XOR of Two Numbers in an Array

Medium

Given a non-empty array of numbers, a0, a1, a2, … , an-1, where 0 ≤ ai < 231.

Find the maximum result of ai XOR aj, where 0 ≤ i, j < n.

Could you do this in O(n) runtime?

Example:

Input: [3, 10, 5, 25, 2, 8]

Output: 28

Explanation: The maximum result is 5 ^ 25 = 28.

题目大意：给一个数组，要求找出两个数，两数异或的结果最大。

解题思路：我们可以一位一位的提取两个数，计算两个数的异或和，如果高位为1，则其他异或高位为0的数对不可能有机会成为答案。

```c++
class Solution {
public:
    int findMaximumXOR(vector<int>& nums) {
        int ans = 0;
        unsigned int mask = 0;
        /*
            3    10		 5		25		 2    8
            11	 1010	101		11001	10	1000
            10000  (25)
            11000  (25)(3, 5, 2)
            11100  (25)(3, 2)
        find the contributor to each bit '1'
        */
        for (int b = 30; b >= 0; b--) {
            mask |= (1 << b); //mask to extract the b-th bit

            //extract the prefix of each num and preserved in prefix
            unordered_set<unsigned int> prefix;
            for (auto ele : nums) {
                prefix.insert(ele & mask);
            }

            //candiate contains '1' at the b-th bit, check if
            //	we can have a prefix to contribute this '1'
            unsigned int candidate = ans | (1 << b);
            //check in all prefixes
            for (auto p : prefix) {
                //if exist one prefix - p that XOR candiate also in prefix set
                //  p (0) ^ s (1) = candidate (1) => candidate(1) ^ p(0) = s (1)
                if (prefix.count(candidate ^ p) > 0) {
                    ans = candidate; //candidate can have, update ans
                    break;
                }
            }
        }

        return ans;        
    }
};
```
测试一下，
```
Success
Details
Runtime: 108 ms, faster than 50.91% of C++ online submissions for Maximum XOR of Two Numbers in an Array.
Memory Usage: 36.7 MB, less than 31.07% of C++ online submissions for Maximum XOR of Two Numbers in an Array.
```
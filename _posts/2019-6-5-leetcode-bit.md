---
layout: post
title: LeetCode专题-位运算
tags: [Leetcode]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

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

<span id="jump401"></span>

401. Binary Watch
Easy

A binary watch has 4 LEDs on the top which represent the hours (0-11), and the 6 LEDs on the bottom represent the minutes (0-59).

Each LED represents a zero or one, with the least significant bit on the right.

![For example, the above binary watch reads "3:25".](https://upload.wikimedia.org/wikipedia/commons/8/8b/Binary_clock_samui_moon.jpg)

Given a non-negative integer n which represents the number of LEDs that are currently on, return all possible times the watch could represent.

```
Example:

Input: n = 1
Return: ["1:00", "2:00", "4:00", "8:00", "0:01", "0:02", "0:04", "0:08", "0:16", "0:32"]
```

Note:

    The order of output does not matter.
    The hour must not contain a leading zero, for example "01:00" is not valid, it should be "1:00".
    The minute must be consist of two digits and may contain a leading zero, for example "10:2" is not valid, it should be "10:02".


题目大意：有一个用二进制位LED表示时：分的手表，要求列出有num个位LED亮时，可能的时间。

解题思路：从惯性思维的角度，这道题可以用DFS做，但是用DFS做，二进制转时间字符串需要花费很多时间和空间，反而不如直接穷举所有可能的时间好。

DFS解法
```c++
const int N = 10;
void readBinaryWatchDFS(int n, int start, bitset<N> &bits, vector<bitset<N>> &ans){
    if(start == N || n == 0){
        if(n == 0){
            ans.push_back(bits);
        }
        return;
    }

    for(int i = start; i < N; i++){
        bits[i] = true;
        readBinaryWatchDFS(n-1, i+1, bits, ans);
        bits[i] = false;
    }
}

vector<string> readBinaryWatch(int num) {
    string init(N, '0');
    bitset<N> bits(init);
    vector<bitset<N>> bits_ans;
    vector<string> ans;
    readBinaryWatchDFS(num, 0, bits, bits_ans);
    for(auto & bits : bits_ans){
        string s_bits = bits.to_string();
        bitset<4> hour_bit(s_bits.substr(0, 4)), 
                  min_bit(s_bits.substr(4, 6));
        unsigned long hour = hour_bit.to_ulong(),
                 min = min_bit.to_ulong();
        string s_hour = hour < 10 ? '0' + to_string(hour) : to_string(hour),
               s_min = min < 10 ? '0' + to_string(min) : to_string(min);
        ans.push_back(s_hour + ':' + s_min);
    }
    return move(ans);
}
```
用上面的DFS解法在大数据集合会超时无法完成。

暴力解法
```c++
class Solution {
public:
    vector<string> readBinaryWatch(int num) {
        vector<string> ans;
        for(unsigned long h = 0; h < 12; h++){
            for(unsigned long m = 0; m < 60; m++){
                bitset<4> hour(h);
                bitset<6> min(m);
                if(hour.count() + min.count() == num){
                    ans.push_back(to_string(h) + ":" + (m < 10 ? '0' + to_string(m) : to_string(m)));
                }
            }
        }
        return move(ans);        
    }
};
```
测试效果，
```
Success
Details
Runtime: 4 ms, faster than 86.95% of C++ online submissions for Binary Watch.
Memory Usage: 8.6 MB, less than 68.73% of C++ online submissions for Binary Watch.
```
<span id="jump89"></span>

# 89. Gray Code

Medium

The gray code is a binary numeral system where two successive values differ in only one bit.

Given a non-negative integer n representing the total number of bits in the code, print the sequence of gray code. A gray code sequence must begin with 0.

```
Example 1:

Input: 2
Output: [0,1,3,2]
Explanation:
00 - 0
01 - 1
11 - 3
10 - 2

For a given n, a gray code sequence may not be uniquely defined.
For example, [0,2,3,1] is also a valid gray code sequence.

00 - 0
10 - 2
11 - 3
01 - 1

Example 2:

Input: 0
Output: [0]
Explanation: We define the gray code sequence to begin with 0.
             A gray code sequence of n has size = 2n, which for n = 0 the size is 20 = 1.
             Therefore, for n = 0 the gray code sequence is [0].

```

题目大意：格雷码据说是为了通信时避免出错而产生的，在每次+1时只变化一位。要求将拥有n位的格雷码输出。

解题思路：新格雷码是在旧格雷码的基础上产生的，从第一位开始，每次在原有格雷码的基础上，将第i位设为1，得到新码。

```c++
class Solution {
public:
    vector<int> grayCode(int n) {
        vector<int> ans = {0};
        // each loop generate code with i bit 1
        for(int i = 0; i < n; i++){
            int len = ans.size(); //new code would be generated based on previous code
            /*
             e.g.  '0000' -> '0001'  (set 1 at 1st bit)
                   '0000', '0001' -> '0010', '0011' (set 1 at 2nd bit)
                   '0000', '0001','0010', '0011' ->
                        '0100', '0101', '0110', '0111' (set 1 at 3rd bit)
                   ....
                   totally
            */
            for(int j = len - 1; j >= 0; j--){
                // generate new code with one bit set on old code
                ans.push_back(ans[j] | 1 << i);
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
Runtime: 4 ms, faster than 93.54% of C++ online submissions for Gray Code.
Memory Usage: 8.7 MB, less than 39.76% of C++ online submissions for Gray Code.
```
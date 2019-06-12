---
layout: post
title: LeetCode专题-哈希表
---

## 423. Reconstruct Original Digits from English

Medium

Given a non-empty string containing an out-of-order English representation of digits 0-9, output the digits in ascending order.

Note:

    Input contains only lowercase English letters.
    Input is guaranteed to be valid and can be transformed to its original digits. That means invalid inputs such as "abc" or "zerone" are not permitted.
    Input length is less than 50,000.

Example 1:

Input: "owoztneoer"

Output: "012"

题目大意：一堆代表数字的英文单词被打乱，要求还原出它代表的数字。

解题思路：将英文单词的字符记录在哈希表中，一个个找出其中的数字。

```c++
class Solution {
public:
    string originalDigits(string s) {
        const vector<string> digits = { "zero", "one", "two", "three", "four", "five", "six", "seven", "eight", "nine" };
        unordered_map<char, int> freq;
        string ans;
        for (auto c : s) {
            freq[c]++;
        }

        for (size_t i = 0; i < digits.size(); i++) {
            int nDigitsNum = INT_MAX;
            for (auto d : digits[i]) {
                if (freq.count(d) == 0) {
                    nDigitsNum = INT_MAX; //we can have none
                    break;
                }
                else {
                    // the minimum num of digit i we can have
                    nDigitsNum = min(nDigitsNum, freq[d]);
                }
            }

            if (nDigitsNum == INT_MAX) {
                continue;
            }

            //reduce the available digit characters
            for (auto d : digits[i]) {
                freq[d] -= nDigitsNum;
            }

            //append digits
            while(nDigitsNum--)
                ans.append(to_string(i));
        }

        return ans;        
    }
};
```
测试一下，有一些用例还是不能通过测试，
```
Input
"zeroonetwothreefourfivesixseveneightnine"
Output
"01113356"
Expected
"0123456789"
```
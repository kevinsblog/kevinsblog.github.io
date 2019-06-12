---
layout: post
title: LeetCode专题-滑动窗
---

## 424. Longest Repeating Character Replacement

Medium

Given a string that consists of only uppercase English letters, you can replace any letter in the string with another letter at most k times. Find the length of a longest substring containing all repeating letters you can get after performing the above operations.

Note:
Both the string's length and k will not exceed 104.

Example 1:

Input:
s = "ABAB", k = 2

Output:
4

Explanation:
Replace the two 'A's with two 'B's or vice versa.

题目大意：允许k次修改，要求找出最长经修改后只有一种字符的子字符串。

解题思路：利用滑动窗，维持一个字字符串满足条件，从头到尾进行滑动，找出最长的子字符串。

```c++
class Solution {
public:
    int characterReplacement(string s, int k) {
        //the whole string must be valid
        if (k >= (int)s.size()) {
            return (int)s.size();
        }
        unordered_map<char, int> count;
        int start = 0, end = 0;
        int ans = k;
    
        //maintain a sliding window which is a valid subset
        while (end < (int)s.size()) {
            count[s[end]]++; //add the 'end' into subset

            //adjust subset to ensure it is valid
            bool needadjust = true;
            while (needadjust) {
                if (count.size() > 2) { //invalid
                    needadjust = true;
                }
                else if (count.size() == 2) {
                    auto it = count.begin();
                    int firstCnt = it->second,
                        secondCnt = (++it)->second;
                    //valid subset only if one of the character can
                    //	be totally removed
                    if (firstCnt <= k || secondCnt <= k) {
                        needadjust = false;
                    }
                    else { //invalid
                        needadjust = true;
                    }
                }
                else { //valid
                    needadjust = false;
                }

                if (needadjust) {
                    count[s[start]]--;
                    if (count[s[start]] == 0) {
                        count.erase(s[start]); //need to remove items count 0
                    }
                    start++;
                }
            }

            ans = max(ans, end - start + 1);
            end++;
        }

        return ans;
    }
};
```
测试一下，
```
Wrong Answer
Details
Playground Debug
Input
"KRSCDCSONAJNHLBMDQGIFCPEKPOHQIHLTDIQGEKLRLCQNBOHNDQGHJPNDQPERNFSSSRDEQLFPCCCARFMDLHADJADAGNNSBNCJQOF"
4
Output 4
Expected 7
```
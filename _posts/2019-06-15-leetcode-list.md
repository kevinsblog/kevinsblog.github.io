---
layout: post
title: LeetCode专题-链表
tags: [Leetcode]
bigimg: /img/path.jpg
comments: true
---

# 目录 

[82. Remove Duplicates from Sorted List II](#jump82)

<span id="jump82"></span>

# 82. Remove Duplicates from Sorted List II

Medium

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

```
Example 1:

Input: 1->2->3->3->4->4->5
Output: 1->2->5

Example 2:

Input: 1->1->1->2->3
Output: 2->3
```

题目大意：在一个链表中，凡是存在键值重复出现的节点，全部删除掉。

解题思路：先遍历一次链表，记录每个键值出现的次数，再遍历一次链表，将需要删除的节点删除掉。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if(head == nullptr || head->next == nullptr){
            return head;
        }
        ListNode dummy(0);
        dummy.next = head;
        unordered_map<int, int> freq;
        auto prev = &dummy, curr = dummy.next;
        //record node with duplicates
        while(curr != nullptr){
            freq[curr->val]++;
            curr = curr->next;
        }

        curr = dummy.next;
        //delete duplicate node
        while(curr != nullptr){
            auto next = curr->next;
            if(freq[curr->val] > 1){
                prev->next = next;
                delete curr;
            }else{
                prev = curr;
            }
            curr = next;
        }
        return dummy.next;        
    }
};
```
测试一下，
```
Success
Details
Runtime: 8 ms, faster than 93.29% of C++ online submissions for Remove Duplicates from Sorted List II.
Memory Usage: 10.1 MB, less than 5.03% of C++ online submissions for Remove Duplicates from Sorted List II.
```

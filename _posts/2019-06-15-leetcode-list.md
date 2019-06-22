---
layout: post
title: LeetCode专题-链表
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

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
<span id="jump143"></span>

# 143. Reorder List

Medium

Given a singly linked list L: L0→L1→…→Ln-1→Ln,
reorder it to: L0→Ln→L1→Ln-1→L2→Ln-2→…

You may not modify the values in the list's nodes, only nodes itself may be changed.

```
Example 1:

Given 1->2->3->4, reorder it to 1->4->2->3.

Example 2:

Given 1->2->3->4->5, reorder it to 1->5->2->4->3.
```

题目大意：对一个链表，将它的后半部分逆序后插入前半部分得到新链表。

解题思路：用快慢指针将链表分成两部分，将后半部分逆序，再重新组合链表。

```c++
class Solution {
public:
    void reorderList(ListNode* head) {
        //inline function to reverse list
        auto reverseList = [](ListNode* head){
            ListNode *prev = nullptr, *curr = head, *next = nullptr;
            while(curr != nullptr){
                next = curr->next;
                curr->next = prev;
                prev = curr;
                curr = next;
            }
            return prev;
        };    

        //no need to reorder if list has less then 3 nodes
        if(head == nullptr || head->next == nullptr || head->next->next == nullptr){
            return;
        }
        
        //find the mid node
        ListNode *fast = head, *slow = head;
        while(fast->next != nullptr && fast->next->next != nullptr){
            fast = fast->next->next;
            slow = slow->next;
        }

        //split the list into two
        auto mid = slow->next;
        slow->next = nullptr;
        mid = reverseList(mid); //reverse the second half
        
        //combine the two lists
        while(head && mid){
            auto next = head->next;
            head->next = mid;
            mid = mid->next;
            head->next->next = next;
            head = next;
        }        
    }
};
```
测试一下，
```
Success
Details
Runtime: 44 ms, faster than 98.09% of C++ online submissions for Reorder List.
Memory Usage: 12.2 MB, less than 54.52% of C++ online submissions for Reorder List.
```


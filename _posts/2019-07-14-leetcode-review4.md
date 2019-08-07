---
layout: post
title: LeetCode专题 - 纯C实现复习题集合(四)
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 14. 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 ""。

```
示例 1:

输入: ["flower","flow","flight"]
输出: "fl"

示例 2:

输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
```

说明:

所有输入只包含小写字母 a-z 。

{% highlight c linenos %}
char * longestCommonPrefix(char ** strs, int strsSize){
    if(!strs || strsSize == 0){
        return "";
    }else if(strsSize == 1){
        return strs[0];
    }
    
    int idx = 0;
    const char *pbase = strs[0], *pcur = NULL;
    while(true){
        for(int i = 1; i < strsSize; i++){
            pcur = strs[i];
            if(!*(pbase + idx) || !*(pcur + idx) || 
                *(pbase + idx) != *(pcur + idx)){
                goto END;
            }
        }
        idx++;
    }
    
END:
    if(idx == 0){
        return "";
    }
    char * pret = (char *)malloc(sizeof(char)*idx+1);
    strncpy(pret, pbase, idx);
    pret[idx] = '\0';
    return pret;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :8 ms, 在所有 C 提交中击败了69.44% 的用户
内存消耗 :7 MB, 在所有 C 提交中击败了82.49%的用户
```

# 344. 反转字符串

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 char[] 的形式给出。
不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。
你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。

```
示例 1：

输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]

示例 2：

输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]
```
{% highlight c linenos %}
void reverseString(char* s, int sSize){
    if(!s || sSize <= 1){
        return;
    }
    
    char *pbeg = s, *pend = s + sSize - 1;
    char tmp;
    while(pbeg < pend){
        tmp = *pbeg;
        *pbeg = *pend;
        *pend = tmp;
        pbeg++;
        pend--;
    }
    
    return;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :64 ms, 在所有 C 提交中击败了97.30% 的用户
内存消耗 :13.7 MB, 在所有 C 提交中击败了75.23%的用户
```

# 415. 字符串相加

给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和。

注意：
    num1 和num2 的长度都小于 5100.
    num1 和num2 都只包含数字 0-9.
    num1 和num2 都不包含任何前导零。
    你不能使用任何內建 BigInteger 库， 也不能直接将输入的字符串转换为整数形式。

{% highlight c linenos %}
void reverseString(char* s, int sSize){
    if(!s || sSize <= 1){
        return;
    }
    
    char *pbeg = s, *pend = s + sSize - 1;
    char tmp;
    while(pbeg < pend){
        tmp = *pbeg;
        *pbeg = *pend;
        *pend = tmp;
        pbeg++;
        pend--;
    }
    
    return;
}

int max(int a, int b){
    return a >= b ? a : b;
}

char * addStrings(char * num1, char * num2){
    int res_len = max(strlen(num1), strlen(num2)) + 2;
    char *res = (char *)calloc(sizeof(char), res_len);
    char *cur = res;
    int carry = 0, n1, n2, n3 = 0;
    reverseString(num1, strlen(num1));
    reverseString(num2, strlen(num2));
    while(*num1 || *num2){
        n1 = *num1 ? *num1 - '0' : 0;
        n2 = *num2 ? *num2 - '0' : 0;
        *cur = (n1 + n2 + carry)%10 + '0';
        carry = (n1 + n2 + carry)/10;
        if(*num1){num1++;}
        if(*num2){num2++;}
        cur++;
    }
    if(carry > 0){
        *cur = carry + '0';
        cur++;
    }
    reverseString(res, strlen(res));
    return res;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :0 ms, 在所有 C 提交中击败了100.00% 的用户
内存消耗 :7 MB, 在所有 C 提交中击败了95.31%的用户
```

# 147. 对链表进行插入排序

对链表进行插入排序。

插入排序的动画演示如上。从第一个元素开始，该链表可以被认为已经部分排序（用黑色表示）。
每次迭代时，从输入数据中移除一个元素（用红色表示），并原地将其插入到已排好序的链表中。

插入排序算法：

    插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
    每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
    重复直到所有输入数据插入完为止。

![](https://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-example-300px.gif)
```
示例 1：

输入: 4->2->1->3
输出: 1->2->3->4

示例 2：

输入: -1->5->3->4->0
输出: -1->0->3->4->5
```

{% highlight c linenos %}
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* insertionSortList(struct ListNode* head){
    if(!head || !head->next){
        return head;
    }
    
    struct ListNode dummy = {INT_MIN, head};
    struct ListNode *pre = NULL, *cur = head->next, *nxt = NULL;
    head->next = NULL;
    while(cur){
        nxt = cur->next;
        pre = &dummy;
        //find a pos for insert
        while(pre->next && pre->next->val < cur->val){
            pre = pre->next;
        }
        if(pre->next){
            cur->next = pre->next; //insert
        }else{
            cur->next = NULL; //append
        }
        pre->next = cur;
        cur = nxt;
    }
    
    return dummy.next;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :52 ms, 在所有 C 提交中击败了64.36% 的用户
内存消耗 :8.1 MB, 在所有 C 提交中击败了36.96%的用户
```

# 148. 排序链表

在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

```
示例 1:

输入: 4->2->1->3
输出: 1->2->3->4

示例 2:

输入: -1->5->3->4->0
输出: -1->0->3->4->5
```

代码与上题相同，
测试一下，
```
执行结果：
通过
显示详情
执行用时 :1008 ms, 在所有 C 提交中击败了15.38% 的用户
内存消耗 :9.9 MB, 在所有 C 提交中击败了96.67%的用户
```

# 709. 转换成小写字母

实现函数 ToLowerCase()，该函数接收一个字符串参数 str，并将该字符串中的大写字母转换成小写字母，之后返回新的字符串。

```
示例 1：

输入: "Hello"
输出: "hello"

示例 2：

输入: "here"
输出: "here"

示例 3：

输入: "LOVELY"
输出: "lovely"
```

{% highlight c linenos %}
char * toLowerCase(char * str){
    if(!str || !*str){
        return str;
    }
    char *ret = str;
    while(*str){
        if(*str >= 'A' && *str <= 'Z'){
            *str -= 'A' - 'a';
        }
        str++;
    }
    return ret;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :4 ms, 在所有 C 提交中击败了83.76% 的用户
内存消耗 :6.8 MB, 在所有 C 提交中击败了5.57%的用户
```

# 557. 反转字符串中的单词 III

给定一个字符串，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

```
示例 1:

输入: "Let's take LeetCode contest"
输出: "s'teL ekat edoCteeL tsetnoc" 
```

注意：在字符串中，每个单词由单个空格分隔，并且字符串中不会有任何额外的空格。

{% highlight c linenos %}
char * reverseWords(char * s){
    if(!s || !*s){
        return s;
    }
    
    char *beg = NULL, *end = NULL, *cur = s;
    char tmp;
    while(*cur){
        beg = cur;
        //find the head of a word
        while(*beg && isspace(*beg)){
            beg++;
        }
        
        //find the tail of a word
        end = beg;
        while(*end && !isspace(*end)){
            end++;
        }
        end--;
        
        //reverse the word
        cur = end + 1;
        while(beg < end){
            tmp = *beg;
            *beg = *end;
            *end = tmp;
            beg++;
            end--;
        }
    }
    
    return s;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :12 ms, 在所有 C 提交中击败了86.12% 的用户
内存消耗 :8.3 MB, 在所有 C 提交中击败了42.16%的用户
```

{% highlight c linenos %}
{% endhighlight %}
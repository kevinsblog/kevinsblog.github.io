---
layout: post
title: LeetCode专题 - 复习题集合(三)
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 257. 二叉树的所有路径

给定一个二叉树，返回所有从根节点到叶子节点的路径。

说明: 叶子节点是指没有子节点的节点。

示例:

```
输入:

   1
 /   \
2     3
 \
  5

输出: ["1->2->5", "1->3"]
```
解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3

{% highlight c++ linenos %}
class Solution {
public:
    void dfs(TreeNode* root, vector<int> &path, vector<vector<int>> &seqs){
        if(!root){
            return;
        }
        
        path.push_back(root->val);
        if(!root->left && !root->right){
            seqs.push_back(path);
            path.pop_back();
            return;
        }
        
        dfs(root->left, path, seqs);
        dfs(root->right, path, seqs);
        path.pop_back();
    }
    
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> ans;
        vector<vector<int>> seqs;
        vector<int> path;
        dfs(root, path, seqs);
        for(auto & seq : seqs){
            string s;
            for(int i = 0; i < seq.size(); i++){
                if(i != seq.size() - 1){
                    s += to_string(seq[i]) + "->";
                }else{
                    s += to_string(seq[i]);
                }
            }
            
            if(!s.empty()){
                ans.push_back(s);
            }
        }
        
        return move(ans);
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :4 ms, 在所有 C++ 提交中击败了95.14% 的用户
内存消耗 :10.7 MB, 在所有 C++ 提交中击败了97.52%的用户
```

# 114. 二叉树展开为链表

给定一个二叉树，原地将它展开为链表。

```
例如，给定二叉树

    1
   / \
  2   5
 / \   \
3   4   6

将其展开为：

1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```

{% highlight c++ linenos %}
class Solution {
public:
    void flatten(TreeNode* root) {
        if(!root)
            return;
        
        //flatten the left as well as right
        flatten(root->left);
        flatten(root->right);
        auto t = root->right;
        //root->right->left
        root->right = root->left;
        root->left = nullptr; //set null at list tail
        while(root->right){
            root = root->right;
        }
        root->right = t;
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :0 ms, 在所有 C++ 提交中击败了100.00% 的用户
内存消耗 :9.6 MB, 在所有 C++ 提交中击败了90.37%的用户
```

# 169. 求众数

给定一个大小为 n 的数组，找到其中的众数。众数是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在众数。

```
示例 1:

输入: [3,2,3]
输出: 3

示例 2:

输入: [2,2,1,1,1,2,2]
输出: 2
```

{% highlight c++ linenos %}
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        if(nums.empty()){
            throw new exception(); //invalid input
        }
        
        int cnt = 1, candidate = nums[0];
        for(int i = 1; i < nums.size(); i++){
            if(nums[i] == candidate){
                cnt++;
            }else{
                cnt--;
            }
            
            if(cnt == 0){
                candidate = nums[i]; //change candidate
                cnt = 1;
            }
        }
        
        if(cnt == 0){
            throw new exception(); //no answer
        }
        return candidate;
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :32 ms, 在所有 C++ 提交中击败了58.28% 的用户
内存消耗 :10.9 MB, 在所有 C++ 提交中击败了92.89% 的用户
```

# 215. 数组中的第K个最大元素

在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

```
示例 1:

输入: [3,2,1,5,6,4] 和 k = 2
输出: 5

示例 2:

输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```
说明:
你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

{% highlight c++ linenos %}
class Solution {
public:
    int partition(vector<int>& nums, int k, int beg, int end){
        int i = beg, j = end, flag = nums[beg];
        //partion in ascending order
        while(i < j){
            while(i < j && nums[j] >= flag){
                j--; //advance if in order
            }
            swap(nums[i], nums[j]); //swap if not in order
            
            while(i < j && nums[i] <= flag){
                i++;
            }
            swap(nums[i], nums[j]);
        }
        
        if(i == k){
            return flag;
        }else if(i > k){
            return partition(nums, k, beg, i-1);
        }else{
            return partition(nums, k, i+1, end);
        }
    }
    
    int findKthLargest(vector<int>& nums, int k) {
        if(k > nums.size()){
            throw new exception();
        }
        return partition(nums, nums.size() - k, 0, nums.size()-1);
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :36 ms, 在所有 C++ 提交中击败了39.17% 的用户
内存消耗 :10.9 MB, 在所有 C++ 提交中击败了9.20%的用户
```

解法二，
{% highlight c++ linenos %}
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        set<int> largestNum;
        if(k < 1 || k > nums.size()){
            throw new exception();
        }
        
        for(auto & num : nums){
            if(largestNum.size() < k){
                largestNum.insert(num);
            }else{
                if(num > *largestNum.begin()){
                    largestNum.erase(largestNum.begin());
                    largestNum.insert(num);
                }
            }
        }
        
        return *largestNum.begin();
    }
};
{% endhighlight %}

# 53. 最大子序和

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

```
示例:

输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```
进阶:

如果你已经实现复杂度为 O(n) 的解法，尝试使用更为精妙的分治法求解。

{% highlight c++ linenos %}
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.size() == 0){
            return 0;
        }
        
        int curSum = 0, maxSum = INT_MIN;
        for(auto & num : nums){
            if(curSum <= 0){
                curSum = num;
            }else{
                curSum += num;
            }
            
            if(curSum > maxSum){
                maxSum = curSum;
            }
        }
        
        return maxSum;
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :20 ms, 在所有 C++ 提交中击败了24.30% 的用户
内存消耗 :9.1 MB, 在所有 C++ 提交中击败了94.27%的用户
```

# 191. 位1的个数

编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’ 的个数（也被称为汉明重量）。

```
示例 1：

输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。

示例 2：

输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。

示例 3：

输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```

提示：
    请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
    在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在上面的 示例 3 中，输入表示有符号整数 -3。

进阶:
如果多次调用这个函数，你将如何优化你的算法？

{% highlight c++ linenos %}
class Solution {
public:
    int hammingWeight(uint32_t n) {
        uint32_t mask = 0x01;
        int cnt = 0;
        while(mask){
            if(n & mask){
                cnt++;
            }
            mask <<= 1;
        }
        return cnt;
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :4 ms, 在所有 C++ 提交中击败了89.64% 的用户
内存消耗 :8 MB, 在所有 C++ 提交中击败了62.80%的用户
```

# 8. 字符串转换整数 (atoi)

请你来实现一个 atoi 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

说明：

假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，qing返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

```
示例 1:

输入: "42"
输出: 42

示例 2:

输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。

示例 3:

输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。

示例 4:

输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。

示例 5:

输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

{% highlight c linenos %}
int myAtoi(char * str){
    int sign = 1;
    int res = 0;
    while(*str != '\0' && isspace(*str)){
        str++;
    }
    if(*str == '-'){
        sign = -1;
        str++;
    }else if(*str == '+'){
        str++;
    }
    
    while(*str != '\0' && !isspace(*str)){
        if(*str < '0' || *str > '9'){
            break;
        }
        
        if(res > (INT_MAX - (*str - '0'))/10){
            return sign == 1 ? INT_MAX : INT_MIN;
        }
        
        res = 10*res + (*str - '0');
        str++;
    }
    
    return res*sign;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :8 ms, 在所有 C 提交中击败了70.74% 的用户
内存消耗 :6.9 MB, 在所有 C 提交中击败了67.90%的用户
```

# 28. 实现strStr()

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

```
示例 1:

输入: haystack = "hello", needle = "ll"
输出: 2

示例 2:

输入: haystack = "aaaaa", needle = "bba"
输出: -1
```

说明:

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

{% highlight c linenos %}
int strStr(char * haystack, char * needle){
    if(!haystack || !needle){
        return 0; //null str
    }
    if(!*haystack && !*needle){
        return 0; //empty str
    }
    const char *ph = haystack, *pn = needle, *start = NULL;
    while(*ph){
        start = ph; //记录主串开始扫描的位置
        pn = needle;
        do{
            if(!*pn){ //子串扫描完成，找到答案
                return start - haystack;
            }
        }while(*pn++ == *ph++); //比较，推进子串和主串
        ph = start+1;
    }
    
    return -1;
}
{% endhighlight %}

# 125. 验证回文串

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。

说明：本题中，我们将空字符串定义为有效的回文串。

```
示例 1:

输入: "A man, a plan, a canal: Panama"
输出: true

示例 2:

输入: "race a car"
输出: false
```

{% highlight c linenos %}
bool isPalindrome(char * s){
    const char *beg = s, *end = s;
    while(*end != '\0'){
        end++;
    }
    end--; //move to the last character
    
    while(beg < end){
        //skip no alp or num char
        while(*beg != '\0' && !isalnum(*beg)){beg++;}
        while(end > beg && !isalnum(*end)){end--;}
        if(end <= beg){
            break;
        }
        //check if ok
        if(*beg - *end == 0 || 
          (isalpha(*beg) && isalpha(*end) && abs(*beg - *end) == 'a' - 'A' )){      
        }else{return false;}
        beg++;
        end--;
    }
    
    return true;
}
{% endhighlight %}
测试一下，
```
执行结果：
通过
显示详情
执行用时 :8 ms, 在所有 C 提交中击败了80.79% 的用户
内存消耗 :7.6 MB, 在所有 C 提交中击败了9.54%的用户
```
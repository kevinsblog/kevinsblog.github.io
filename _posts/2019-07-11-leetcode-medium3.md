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

{% highlight c++ linenos %}
{% endhighlight %}
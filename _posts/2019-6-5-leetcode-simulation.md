---
layout: post
title: LeetCode专题-模拟计算
---

## 目录

1041. Robot Bounded In Circle
989. Add to Array-Form of Integer
838. Push Dominoes
390. Elimination Game
419. Battleships in a Board
43. Multiply Strings
54. Spiral Matrix
59. Spiral Matrix II

## 1041. Robot Bounded In Circle

Easy

On an infinite plane, a robot initially stands at (0, 0) and faces north.  The robot can receive one of three instructions:

    "G": go straight 1 unit;
    "L": turn 90 degrees to the left;
    "R": turn 90 degress to the right.

The robot performs the instructions given in order, and repeats them forever.

Return true if and only if there exists a circle in the plane such that the robot never leaves the circle.

Example 1:

Input: "GGLLGG"
Output: true
Explanation: 
The robot moves from (0,0) to (0,2), turns 180 degrees, and then returns to (0,0).
When repeating these instructions, the robot remains in the circle of radius 2 centered at the origin.

题目大意：对一个机器人发送一系列重复的指令，要求判断机器人会不会回到原点。

解题思路：指令是重复的，因此只要机器人不朝向北方，就有机会回到原点

```python
class Solution:
    def isRobotBounded(self, instructions: str) -> bool:
        x = 0
        y = 0
        dir = 0 #direction - N W S E
        #offset for each direction
        dx = [0, -1, 0, 1]
        dy = [1, 0, -1, 0]
        for c in instructions:
            if c == 'G': #go ahead by dir
                x += dx[dir]
                y += dy[dir]
            elif c == 'L': #turn left
                dir = (dir + 3)%4
            elif c == 'R': #turn right
                dir = (dir + 1)%4
        return (x == 0 and y == 0) or dir != 0 #if go back to origin or not facing north
        
```
测试一下
```
Success
[Details]
Runtime: 32 ms, faster than 86.36% of Python3 online submissions for Robot Bounded In Circle.
Memory Usage: 13.2 MB, less than 100.00% of Python3 online submissions for Robot Bounded In Circle.
```

## 838. Push Dominoes

Medium

There are `N` dominoes in a line, and we place each domino vertically upright.

In the beginning, we simultaneously push some of the dominoes either to the left or to the right.

![image](http://upload-images.jianshu.io/upload_images/11294958-82eac14ccb37a57b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

After each second, each domino that is falling to the left pushes the adjacent domino on the left.

Similarly, the dominoes falling to the right push their adjacent dominoes standing on the right.

When a vertical domino has dominoes falling on it from both sides, it stays still due to the balance of the forces.

For the purposes of this question, we will consider that a falling domino expends no additional force to a falling or already fallen domino.

Given a string "S" representing the initial state. `S[i] = 'L'`, if the i-th domino has been pushed to the left; `S[i] = 'R'`, if the i-th domino has been pushed to the right; `S[i] = '.'`, if the `i`-th domino has not been pushed.

Return a string representing the final state. 

**Example 1:**

**Input:** ".L.R...LR..L.."
**Output:** "LL.RR.LLRRLL.."

**Example 2:**

**Input:** "RR.L"
**Output:** "RR.L"
**Explanation:** The first domino expends no additional force on the second domino.

题目大意：对于一组多米诺骨牌，给定一个初始化推的指令，求出最终多米诺骨牌的状态。

解题思路：通过推的指令，计算每一个骨牌的状态。

```python
class Solution(object):
    def pushDominoes(self, D):
        """
        :type dominoes: str
        :rtype: str
        """
        max_int = 9999999
        #record the distance from current dominoe to nearest push down one(left and right)
        left_steps = [max_int for x in range(len(D))]
        right_steps = [max_int for x in range(len(D))]

        for i in range(len(D)):
            if D[i] == 'L':
                #if occur one push down domine
                left_steps[i] = 0
                #for all following domine that influenced
                for j in range(i-1, -1, -1):
                    if D[j] != '.': #only influence '.'
                        break
                    left_steps[j] = left_steps[j+1]+1
            elif D[i] == 'R':
                right_steps[i] = 0
                for j in range(i+1, len(D)):
                    if D[j] != '.':
                        break
                    right_steps[j] = right_steps[j-1]+1

        #simulate the push work
        ND = ''
        for i in range(len(D)):
            if left_steps[i] < right_steps[i]:
                ND += 'L'
            elif left_steps[i] > right_steps[i]:
                ND += 'R'
            else:
                ND += '.'

        return ND
```
测试一下，算法应该是对的，但是用python实现会超时，用golang或者cpp可以通过所有测试用例。
```
Time Limit Exceeded
[Details]
```

## 989. Add to Array-Form of Integer

Easy

For a non-negative integer X, the array-form of X is an array of its digits in left to right order.  For example, if X = 1231, then the array form is [1,2,3,1].

Given the array-form A of a non-negative integer X, return the array-form of the integer X+K.

```
Example 1:

Input: A = [1,2,0,0], K = 34
Output: [1,2,3,4]
Explanation: 1200 + 34 = 1234
```

题目大意：两数相加，被加数以数组的形式提供。

解题思路：模拟手写相加的方式，将进位保存在加数上。

```python3
class Solution(object):
    def addToArrayForm(self, nums, k):
        """
        :type A: List[int]
        :type K: int
        :rtype: List[int]
        """
        nums.reverse()
        ans = []
        for i in range(len(nums)):
            k += nums[i]
            ans.append(k%10)
            k //= 10

        while k > 0:
            ans.append(k%10)
            k //= 10

        ans.reverse()
        return ans 
```
测试一下，
```
Success
[Details]
Runtime: 264 ms, faster than 63.74% of Python online submissions for Add to Array-Form of Integer.
Memory Usage: 12.3 MB, less than 17.19% of Python online submissions for Add to Array-Form of Integer.
```

#390. Elimination Game

Medium

There is a list of sorted integers from 1 to n. Starting from left to right, remove the first number and every other number afterward until you reach the end of the list.

Repeat the previous step again, but this time from right to left, remove the right most number and every other number from the remaining numbers.

We keep repeating the steps again, alternating left to right and right to left, until a single number remains.

Find the last number that remains starting with a list of length n.

Example:

Input:
n = 9,
1 2 3 4 5 6 7 8 9
2 4 6 8
2 6
6

Output:
6

题目大意：给一个数组[1:n]，从左到右，再从右到左，每隔一个元素消除一个，以此往复，直到只剩下一个元素。

解题思路：模拟消除元素的过程，直到剩下一个元素为止。

```java
class Solution {
    public int lastRemaining(int n) {
        boolean left = true;
        int remaining = n;
        int step = 1;
        int head = 1;
        while (remaining > 1) {
            if (left || remaining % 2 ==1) {
                head = head + step;
            }
            remaining = remaining / 2;
            step = step * 2;
            left = !left;
        }
        return head;
    }
}     
```
```python
    lst = [i for i in range(1, n + 1)]
    to_right = True
    while len(lst) > 1:
        print(lst)
        if to_right:
            lst = lst[1::2] #from the first node, skip by two
        else:
            lst = lst[len(lst)-2::-2] #from the last node, skip by two
            lst.reverse() #do reverse
        to_right = not to_right

    if len(lst) == 1:
        return lst[0]
    else:
        return -1 #no solution
```
测试一下
```
Success
[Details]
Runtime: 2 ms, faster than 100.00% of Java online submissions for Elimination Game.
Memory Usage: 33.8 MB, less than 93.66% of Java online submissions for Elimination Game.
```

## 419. Battleships in a Board

Medium

Given an 2D board, count how many battleships are in it. The battleships are represented with 'X's, empty slots are represented with '.'s. You may assume the following rules:

    You receive a valid board, made of only battleships or empty slots.
    Battleships can only be placed horizontally or vertically. In other words, they can only be made of the shape 1xN (1 row, N columns) or Nx1 (N rows, 1 column), where N can be of any size.
    At least one horizontal or vertical cell separates between two battleships - there are no adjacent battleships.

Example:

X..X
...X
...X

In the above board there are 2 battleships.

Invalid Example:

...X
XXXX
...X

This is an invalid board that you will not receive - as battleships will always have a cell separating between them.

Follow up:
Could you do it in one-pass, using only O(1) extra memory and without modifying the value of the board?

题目大意：给一个矩阵作为地图描述船的位置和形状，要求求出船的数目。

解题思路：从上往下，从左往右，数出船的数目。

```c++
class Solution {
public:
    int countBattleships(vector<vector<char>>& board) {
        int ans = 0;
        for (int i = 0; i < static_cast<int>(board.size()); i++) {
            for (int j = 0; j < static_cast<int>(board[i].size()); j++) {
                if (board[i][j] == 'X') {
                    if ((i < 1 || board[i - 1][j] == '.') && (j < 1 || board[i][j - 1] == '.')) {
                        ans++;
                    }
                }
            }
        }
        return ans;        
    }
};
```
测试结果，
```
Success
Details
Runtime: 4 ms, faster than 99.66% of C++ online submissions for Battleships in a Board.
Memory Usage: 9.7 MB, less than 45.37% of C++ online submissions for Battleships in a Board.
```

## 43. Multiply Strings

Medium

Given two non-negative integers num1 and num2 represented as strings, return the product of num1 and num2, also represented as a string.

```
Example 1:

Input: num1 = "2", num2 = "3"
Output: "6"

Example 2:

Input: num1 = "123", num2 = "456"
Output: "56088"
```

> Note:
    > The length of both num1 and num2 is < 110.
    > Both num1 and num2 contain only digits 0-9.
    > Both num1 and num2 do not contain any leading zero, except the number 0 itself.
    > You must not use any built-in BigInteger library or convert the inputs to integer directly.

题目大意：以字符串的形式给定两个数组，求出两数的乘积，以字符串的形式返回。

解题思路：模拟手算两数相乘的过程。注意数字的高位是字符串的低位。注意最后结果字符串中零的处理，去除高位的无效零，但是结果为零时，需要保留最后一位零。

```c++
class Solution {
public:
    string multiply(string num1, string num2) {
        size_t m = num1.size(), n = num2.size();
        string ans;
        int sum = 0;
        vector<int> prod(m + n);
        //for (size_t i = m - 1; i >= 0; i--) {
        //	for (size_t j = n - 1; j >= 0; j--) {
        //		size_t low = i + j + 1,
        //			high = i + j;  //in reverse order
        //		sum = (num1[i] - '0') * (num2[j] - '0') + prod[low];
        //		prod[high] += sum / 10;
        //		prod[low] = sum % 10;
        //	}
        //} //do not use size_t in for loop
        for (int i = m - 1; i >= 0; --i) {
            for (int j = n - 1; j >= 0; --j) {
                int p1 = i + j;
                int p2 = i + j + 1;
                int sum = (num1[i] - '0') * (num2[j] - '0') + prod[p2];
                prod[p1] += sum / 10;
                prod[p2] = sum % 10;
            }
        }

        //find the highest valid bit
        size_t idx = 0;
        while (idx < prod.size() - 1 && prod[idx] == 0) {
            idx++;
        }

        //combine the results
        while (idx < prod.size()) {
            ans += to_string(prod[idx++]);
        }

        return move(ans);        
    }
};
```
测试一下，
```
Success
Details
Runtime: 4 ms, faster than 98.40% of C++ online submissions for Multiply Strings.
Memory Usage: 9 MB, less than 61.35% of C++ online submissions for Multiply Strings.
```

## 54. Spiral Matrix

Medium

Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.

```
Example 1:

Input:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
Output: [1,2,3,6,9,8,7,4,5]

Example 2:

Input:
[
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9,10,11,12]
]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

题目大意：以顺时针螺旋的方式打印一个矩阵。

解题思路：模拟顺时针读矩阵的过程，方向：右-下-左-上-右...，用四条边界来控制读的停止。

```c++
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> ans;
        if(matrix.size() == 0) //empty matrix
            return ans;
        int x = 0, y = 0, d = 0;
        int total = matrix.size()*matrix[0].size();
        int right = matrix[0].size() - 1, left = 0, up = 0, down = matrix.size() - 1;
        //print until all elements have been printed
        while(ans.size() < total){
            switch(d){
                case 0: //to right
                    if(y == right){
                        d = (d+1)%4; //change move direction
                        up++; //the uppest row is removed from next loop of reading
                    }else{
                        ans.push_back(matrix[x][y]);
                        y++; //print and advance
                    }
                    break;
                case 1: //to down
                    if(x == down){
                        d = (d+1)%4;
                        right--;
                    }else{
                        ans.push_back(matrix[x][y]);
                        x++;
                    }
                    break;
                case 2: //to left
                    if(y == left){
                        d = (d+1)%4;
                        down--;
                    }else{
                        ans.push_back(matrix[x][y]);
                        y--;
                    }
                    break;
                case 3: //to up
                    if(x == up){
                        d = (d+1)%4;
                        left++;
                    }else{
                        ans.push_back(matrix[x][y]);
                        x--;
                    }
                    break;
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
Runtime: 0 ms, faster than 100.00% of C++ online submissions for Spiral Matrix.
Memory Usage: 8.7 MB, less than 40.35% of C++ online submissions for Spiral Matrix.
```

## 59. Spiral Matrix II

Medium

Given a positive integer n, generate a square matrix filled with elements from 1 to n2 in spiral order.

```
Example:

Input: 3
Output:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
```

题目大意：对一个nxn的矩阵，以顺时针螺旋的方式赋值。

解题思路：思路与上题相同，只是遍历过程中处理的方式有差异。

```c++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        if(n <= 0)
            return vector<vector<int>>();

        vector<vector<int>> ans(n, vector<int>(n, 0));
        int total = n*n, cnt = 0, d = 0;
        int right = n - 1, left = 0, up = 0, down = n - 1;
        int x = 0, y = 0;
        while(cnt < total){
            switch(d){
                case 0:
                    if(y == right){
                        d = (d+1)%4;
                        up++;
                    }else{
                        ans[x][y] = ++cnt; //assign value
                        y++;
                    }
                    break;
                case 1:
                    if(x == down){
                        d = (d+1)%4;
                        right--;
                    }else{
                        ans[x][y] = ++cnt;
                        x++;
                    }
                    break;
                case 2:
                    if(y == left){
                        d = (d+1)%4;
                        down--;
                    }else{
                        ans[x][y] = ++cnt;
                        y--;
                    }
                    break;
                case 3:
                    if(x == up){
                        d = (d+1)%4;
                        left++;
                    }else{
                        ans[x][y] = ++cnt;
                        x--;
                    }
                    break;
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
Runtime: 4 ms, faster than 92.05% of C++ online submissions for Spiral Matrix II.
Memory Usage: 9 MB, less than 50.82% of C++ online submissions for Spiral Matrix II.
```
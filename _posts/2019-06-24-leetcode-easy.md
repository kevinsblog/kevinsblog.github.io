---
layout: post
title: LeetCode专题 - Easy题集合
tags: [Leetcode, C/C++, SQL]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 709. To Lower Case

Easy （字符串题）

Implement function ToLowerCase() that has a string parameter str, and returns the same string in lowercase.

```
Example 1:

Input: "Hello"
Output: "hello"

Example 2:

Input: "here"
Output: "here"

Example 3:

Input: "LOVELY"
Output: "lovely"
```

题目大意：设计一个函数，把字符串中的大写字符改成小写。

解题思路：利用std::transform对字符串区间元素进行修改，设计一个lamda表达式作为算子。

{% highlight c++ linenos %}
class Solution {
public:
    string toLowerCase(string str) {
        transform(str.begin(), str.end(), str.begin() ,[](unsigned char c)                  -> unsigned char { return std::tolower(c); });
        return move(str);
    }
};
{% endhighlight %}
```
Success
Details
Runtime: 4 ms, faster than 77.96% of C++ online submissions for To Lower Case.
Memory Usage: 8.2 MB, less than 34.85% of C++ online submissions for To Lower Case.
```

# 1021. Remove Outermost Parentheses

Easy (字符串题)

A valid parentheses string is either empty (""), "(" + A + ")", or A + B, where A and B are valid parentheses strings, and + represents string concatenation.  For example, "", "()", "(())()", and "(()(()))" are all valid parentheses strings.

A valid parentheses string S is primitive if it is nonempty, and there does not exist a way to split it into S = A+B, with A and B nonempty valid parentheses strings.

Given a valid parentheses string S, consider its primitive decomposition: S = P_1 + P_2 + ... + P_k, where P_i are primitive valid parentheses strings.

Return S after removing the outermost parentheses of every primitive string in the primitive decomposition of S.

```
Example 1:

Input: "(()())(())"
Output: "()()()"
Explanation: 
The input string is "(()())(())", with primitive decomposition "(()())" + "(())".
After removing outer parentheses of each part, this is "()()" + "()" = "()()()".

Example 2:

Input: "(()())(())(()(()))"
Output: "()()()()(())"
Explanation: 
The input string is "(()())(())(()(()))", with primitive decomposition "(()())" + "(())" + "(()(()))".
After removing outer parentheses of each part, this is "()()" + "()" + "()(())" = "()()()()(())".

Example 3:

Input: "()()"
Output: ""
Explanation: 
The input string is "()()", with primitive decomposition "()" + "()".
After removing outer parentheses of each part, this is "" + "" = "".
```

题目大意：题目看起来很长，但意思很简单，对于由一系列括号组成的字符串，要去掉最外面一层括号。

解题思路：用一个栈记录括号属于从外往里数第几层。

{% highlight c++ linenos %}
class Solution {
public:
    string removeOuterParentheses(string S) {
        stack<char> st;
        string ans;
        for(auto & c : S){
            if(c == ')'){ //pop out mathced '('
                st.pop();
            }
            //before re-write parenthese, check if stack is empty
            //  if empty, parenthese is of the outermost
            if(!st.empty()){
                ans += c;
            }
            if(c == '('){ //push in '('
                st.push(c);
            }
        }
        return move(ans);        
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 4 ms, faster than 95.63% of C++ online submissions for Remove Outermost Parentheses.
Memory Usage: 9.1 MB, less than 33.43% of C++ online submissions for Remove Outermost Parentheses.
```

# 804. Unique Morse Code Words

Easy

International Morse Code defines a standard encoding where each letter is mapped to a series of dots and dashes, as follows: "a" maps to ".-", "b" maps to "-...", "c" maps to "-.-.", and so on.

For convenience, the full table for the 26 letters of the English alphabet is given below:

[".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."]

Now, given a list of words, each word can be written as a concatenation of the Morse code of each letter. For example, "cba" can be written as "-.-..--...", (which is the concatenation "-.-." + "-..." + ".-"). We'll call such a concatenation, the transformation of a word.

Return the number of different transformations among all words we have.

```
Example:
Input: words = ["gin", "zen", "gig", "msg"]
Output: 2
Explanation: 
The transformation of each word is:
"gin" -> "--...-."
"zen" -> "--...-."
"gig" -> "--...--."
"msg" -> "--...--."

There are 2 different transformations, "--...-." and "--...--.".
```
Note:

    The length of words will be at most 100.
    Each words[i] will have length in range [1, 12].
    words[i] will only consist of lowercase letters.

题目大意：对于一组由小写字母组成的字符串组，将字符串一一用莫尔斯码表示，求能得到几种不同的
莫尔斯码。

解题思路：把字符串一一转成莫尔斯码，放到二叉树中，看有几个相异的元素。

{% highlight c++ linenos %}
class Solution {
public:
    int uniqueMorseRepresentations(vector<string>& words) {
        const vector<string> alp_tbl = {".-","-...","-.-.","-..",".","..-.",
        "--.","....","..",".---","-.-",".-..","--","-.","---",".--.",
        "--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."};
        set<string> unique_rep;
        for(auto & word : words){
            string rep;
            for(auto & ch : word){
                rep += alp_tbl[ch - 'a'];
            }
            unique_rep.insert(rep);
        }
        return unique_rep.size();        
    }
};
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 8 ms, faster than 38.78% of C++ online submissions for Unique Morse Code Words.
Memory Usage: 8.9 MB, less than 78.78% of C++ online submissions for Unique Morse Code Words.
```

# 595. Big Countries

Easy
SQL Schema

There is a table World
```
+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
```
A country is big if it has an area of bigger than 3 million square km or a population of more than 25 million.

Write a SQL solution to output big countries' name, population and area.

For example, according to the above table, we should output:
```
+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+
```

题目大意：从一个表中检索出"大国"的信息。

解题思路：基本SELECT语句。

{% highlight sql linenos %}
# Write your MySQL query statement below
SELECT name, population, area FROM World WHERE area > 3e6 OR population > 25e6
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 319 ms, faster than 13.06% of MySQL online submissions for Big Countries.
Memory Usage: N/A
```
# 177. Nth Highest Salary

Medium

Write a SQL query to get the nth highest salary from the Employee table.

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

For example, given the above Employee table, the nth highest salary where n = 2 is 200. If there is no nth highest salary, then the query should return null.

```
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```

题目大意：从一张员工信息表中选出第N高的工资。

解题思路：SELECT语句排序。

{% highlight sql linenos %}
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  DECLARE M INT;
  SET M = N - 1;
  RETURN (
      # Write your MySQL query statement below.
      SELECT DISTINCT Salary FROM Employee ORDER BY Salary DESC LIMIT M, 1 
  );
END
{% endhighlight %}
测试一下，
```
Success
Details
Runtime: 234 ms, faster than 13.64% of MySQL online submissions for Nth Highest Salary.
Memory Usage: N/A
```


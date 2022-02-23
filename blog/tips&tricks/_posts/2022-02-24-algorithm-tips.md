---
layout: post
title: Time Complexity and Memory Management
mathjax: true
---

# 1. set, map vs unordered_set, unordered_map
Recently got TLE due to using `unordered_set` and `unordered_map` on a Codeforces contest.\

|                       | set     | unordered_set | map     | unordered_map |
|-----------------------|---------|---------------|---------|---------------|
| Average Case          | O(logN) | O(1)          | O(logN) | O(1)          |
| Worst Case            | O(logN) | O(N)          | O(logN) | O(N)          |
| Implementation Method | BST     | Hash Table    | BST     | Hash Table    |

In a normal situation, using unordered_set and unordered_map would be more efficient.\
However, in Competitive Programming, the test cases may not be in your favor.\
Especially like in Codeforces where there are hacking phases where their whole purpose is to write test cases that make your algorithm fail (By inducing TLE, MLE, edge cases, etc..).

# 2. How many ints inside a megabyte(MB)?
<strong style="text-align: center;">1 MB = 1,048,576 Bytes</strong>

There can be $1,048,576/4=262,144$ ints inside 1 MB.\
There could be approximately 67 million ints inside 256 MB.

# 3. Using double with an int?
While *float* and *double* covers the range of *int* as well as *long long*, they are low in precision.\
Double has 15 to 16 digit precision.\
Float has 7 digit precision.\
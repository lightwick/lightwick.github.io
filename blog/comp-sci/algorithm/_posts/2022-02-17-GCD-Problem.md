---
layout: post
title: "Codeforces Round #761 (Div. 2) B. GCD Problem"
difficulty: 900
problem_tag: math
---

Before going on I want everyone who's reading to know that this is simply *my understanding of the problem* and to be honest, I'm not 100% sure that the logic here is accurate.\
Any and all criticism will be welcomed at *smallyk99@gmail.com*.\
Take this with a grain of salt.\

[Codeforces Round #761 (Div. 2) B. GCD Problem](https://codeforces.com/problemset/problem/1617/B)

> ### Question:
> Given a positive integer n. Find three distinct positive integers a, b, c such that a+b+c=n and gcd(a,b)=c, where gcd(x,y) denotes the greatest common divisor (GCD) of integers x and y.
>
> **Input**
> The input consists of multiple test cases. The first line contains a single integer t (1≤t≤105) — the number of test cases. Description of the test cases follows.
> 
> The first and only line of each test case contains a single integer n (10≤n≤109).
> 
> **Output**
> For each test case, output three distinct positive integers a, b, c satisfying the requirements. If there are multiple solutions, you can print any. We can show that an answer always exists.

a and b can be expressed as $a=cx$, $b=cy$ while $x$ and $y$ are coprime(서로수).

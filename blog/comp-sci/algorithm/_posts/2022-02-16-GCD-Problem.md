---
layout: post
title: "Codeforces Round #761 (Div. 2) B. GCD Problem"
mathjax: true
difficulty: 900
problem_tag: math
---

[Codeforces Round #761 (Div. 2) B. GCD Problem](https://codeforces.com/problemset/problem/1617/B)

# Question:
> Given a positive integer $n$. Find three **distinct** positive integers $a, b, c$ such that $a+b+c=n$ and $gcd(a,b)=c$, where $gcd(x,y)$ denotes the greatest common divisor (GCD) of integers $x$ and $y$.
>
> **Input**
> The input consists of multiple test cases. The first line contains a single integer $t (1≤t≤10^5)$ — the number of test cases. Description of the test cases follows.
> 
> The first and only line of each test case contains a single integer $n (10≤n≤10^9)$.
> 
> **Output**
> For each test case, output three **distinct** positive integers $a, b, c$ satisfying the requirements. If there are multiple solutions, you can print any. We can show that an answer always exists.

# Solution:
Key observation: for $c$ to be the greatest common divisor of $a$ and $b$, the numbers have to be $\alpha$ c ($\alpha$ is a positive integer) apart.
## Proof
Lets say, $a=cx$, $b=cy$ ($x,y$ are coprime). The difference between a and be can be expressed as $\lvert a-b \rvert$.

$$ 5 + 5 $$

$$
\begin{aligned} 
\lvert a-b \rvert &= \lvert cx-cy \rvert \\ &= \lvert c(x-y) \rvert \\ &= c \lvert x-y \rvert 
\end{aligned}
$$

Since all $a,b,c$ must be **distinct**, $\lvert x-y \rvert$ is not 0, meaning it's a positive integer. *Therefore*, this proves the above.

Lets say $\vert a-b \rvert = \theta$. $\theta$ is the distance between $a$ and $b$.\
With the above *key observation*, we can infer the following.\
*The greatest common divisor is among the **divisors of $\theta$***

## Chapter 1 (Even Numbers)
If $c=1$ and the difference between $a$ and $b$ was 1, then the gcd would always be 1 due to the above.
Lets say $c=1$, $a=k$, $b=k+1$ (while $k>=2$, $k$ is an integer). Then $a+b+c=2k+2$\
That would take care of all even numbers bigger than or same as 8.

## Chapter 2 (Odd Numbers)
For numbers like 2,4,8,16, all the divisors them, besides one, are even numbers. So if $a$ and $b$ were to have a difference of 2,4, or 16, and were to be odd numbers, the gcd would always be 1. With this we can take two approaches.
### 2-1 (Solution 1)

$$a=2\alpha a-1, b=2\alpha a+1, c=1 (\alpha>=2 integer)
a+b+c=4\alpha+1
=9,13,17,21,...

a=2\alpha a-1, b=2\alpha a+3, c=1 (\alpha>=2 integer)
a+b+c=4\alpha+3
=11,15,19,23,...$$

And with that all the numbers within the given $n$ are covered.

### 2-2 (Solution 2)
We are still trying to solve while $c=1$.\
Because $c=1$, $a+b=n-1$ and $n$ is an *odd number*, $n-1$ is an $even number$.
Lets say $n-1=2*\alpha (\alpha is a positive integer)$.\

$\alpha$ can be odd, or even. 
***Case #1: $\alpha$ is an even number.***\
$\alpha-1$ and $\alpha+1$ are both odd numbers. They have a difference of 2, and are both odd numbers.\
Ergo, $a=\alpha-1$, $b=\alpha+1$, $c=1$.

***Case #2: $\alpha$ is an odd number.***\
$\alpha-1$ and $\alpha+1$ are both even numbers. Even numbers can't have a difference of 2 for the gcd to be 1.\
$\alpha-2$ and $\alpha+2$ are both odd numbers. They have a difference of 4, and are both odd numbers.\
Ergo, $a=\alpha-2$, $b=\alpha+2$, $c=1$.

## Solution code
### Solution 2
```
using namespace std;

void solve() {
	int n;
	cin >> n;

	int a, b, c = 1;

	if (n % 2 == 0) {
		a = n / 2 - 1;
		b = a + 1;
	}
	else {
		if ((n - 1) % 4 == 0) {
			int alpha = (n - 1) / 4;
			a = 2 * alpha - 1;
			b = 2 * alpha + 1;
		}
		else if ((n - 3) % 4 == 0) {
			int alpha = (n - 3) / 4;
			a = 2 * alpha - 1;
			b = 2 * alpha + 3;
		}
	}

	cout << a << " " << b << " " << c << "\n";
}

int main() {
	ios_base::sync_with_stdio(0);
	int t;
	cin >> t;
	for (int i = 0; i < t; i++) {
		solve();
	}
	return 0;
}
```

### Solution 2
```
void solve() {
	int n;
	cin >> n;

	int a, b, c = 1;

	if (n % 2 == 0) {
		a = n / 2 - 1;
		b = a + 1;
	}
	else {
		int alpha = (n - 1) / 2;
		if (alpha % 2 == 0) {
			a = alpha - 1;
			b = alpha + 1;
		}
		else {
			a = alpha - 2;
			b = alpha + 2;
		}
	}

	cout << a << " " << b << " " << c << "\n";
}

int main() {
	ios_base::sync_with_stdio(0);
	int t;
	cin >> t;
	for (int i = 0; i < t; i++) {
		solve();
	}
	return 0;
}
```
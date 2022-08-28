---
layout: post
title: "Knapsack Problem"
mathjax: true
---

The most basic form of the 'Knapsack problem' the following.

## Problem
There are $n$ items, each of them with a *weight* and a *value*. You can select any number($0 \geq items \geq n$) of items as long as their total *weight* is less then the given limit.\
What is the maximum *value* you can get, considering the *weight* restriction?

## Solution
All of the solutions include dynamic programming.\
`vector<pair<int,int>> vec` has the *weight* and *value* information.

### Solution 1: Recursive
```
// n-th item is in question
// the knapsack can carry x more weight
int kp(int n, int x, vector<pair<int, int>> vec) {
    // if n == 0 there are no more items to consider
    // if x <=0 carrying any more items is impossible; all weights are positive
    if (n == 0 || x <= 0) return 0;
    if (dp[n - 1][x]) return dp[n - 1][x];

    int weight = vec[n - 1].first;
    int value = vec[n - 1].second;

    // Case which the n-th item is not selected.
    int res = kp(n - 1, x, vec);

    // Checks if it is possible to select the n-th item
    if (x - weight >= 0) {
        // Case which the n-th item is selected
        int _res = kp(n - 1, x - time, vec) + score;
        res = max(res, _res);
    }
    dp[n - 1][x] = res;

    return res;
}
```

## Solution 2: Ground-up
```
int kp(int n, int x, vector<pair<int, int>> vec) {
    for (int i = 1; i <= n; i++) {
        int weight = vec[i - 1].first;
        int value = vec[i - 1].second;
        for (int j = 1; j <= x; j++) {
            if (j >= time) {
                dp[i][j] = dp[i - 1][j - weight] + value;
            }
            dp[i][j] = max(dp[i][j], dp[i - 1][j]);
        }
    }
    return dp[n][x];
}
```

## Improved version of Solution 2
```
int kp(int n, int x, vector<pair<int, int>> vec) {
    for (int i = 1; i <= n; i++) {
        int weight = vec[i - 1].first;
        int value = vec[i - 1].second;
        for (int j = x; j >= time; j--) {
            dp[j] = max(dp[j], dp[j - time] + score);
        }
    }
    return dp[x];
}

```

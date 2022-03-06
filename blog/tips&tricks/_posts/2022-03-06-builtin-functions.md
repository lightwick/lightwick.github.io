---
layout: post
title: "Built in Functions of GCC"
---

These are some of the functions that are built in to the gcc compiler.\
The built in functions do not require any headers, as they are part of the compiler.

## 1. __builtin_popcount(n)
```
int n;
__builtin_popcount(n);
```

```
long long n;
__builtin_popcountll(n);
```

It returns the number of `1`s that are in its binary form.\
If `n=7` (`111` in binary), then `__builtin_popcount(n)` will return 3.
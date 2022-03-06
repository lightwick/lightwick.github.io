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
For example, if `int n=7` (`111` in binary), then `__builtin_popcount(n)` will return 3.

## 2. __builtin_clz(n)
```
int n;
__builtin_clz(n);
```

```
long long n;
__builtin_clzll(n);
```

It returns the number of leading `0`s that are in its binary form.\
For example, if `int n=7` (`00000000 00000000 00000000 00000111` in full binary form), then `__builtin_clz(n)` will return `29`.\
> int is 4 bytes (32 bits) and 7 can be expressed as 3 bits (`111`). Therefore `32`-`3`=`29`

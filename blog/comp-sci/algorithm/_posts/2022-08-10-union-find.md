---
layout: post
title: Union Find
---

## Initialization
```
vector<int> parent(n);
```
This is the vector we will work with.\
The `parent` vector will be initialized like the following.
```
for(int i = 0; i < n; i++) {
    parent[i] = i;
}
```

## Find and Merge
```
int find(int k) {
    if (vec[k] == k) {
        return k;
    }
    return get(vec, vec[k]);
}

void merge(int x, int y) {
    int a = find(x);
    int b = find(y);
    vec[a] = b;
}
```
# Segment Trees
A segment tree is a data structure that stores information about array intervals in its nodes. This allows efficient answering of range queries and updates
- **Key Operations:** Build, Query, Update.

# Building a segment tree
<figure style="text-align: center;">
  <img src="assets/diagrams/segment-tree-diagram.png" alt="Segment Tree Diagram" width="70%">
  <figcaption>Figure 1: Example of a Segment Tree</figcaption>
  ```html

**Building a segment tree in C++**
<pre><code class="language-cpp">
#include <iostream>
using namespace std;

const int mxn = 2e5 + 5;
int a[mxn];
vector<int> tree;

void build(int id, int le, int ri) { 
    if (le == ri) {
        tree[id] = a[le];
    } else {
        int mid = (le + ri) / 2;
        build(id * 2, le, mid);
        build(id * 2 + 1, mid + 1, ri);
    }
}
</code></pre>
</figure>


## Example Problem
Given an array, find the sum of elements in a range and update values efficiently.

### Code Implementation
```cpp
#include<bits/stdc++.h>
using namespace std;
...

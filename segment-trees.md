 # Segment Trees

A segment tree is a data structure that stores information about array intervals in its nodes. This allows efficient answering of range queries and updates.

- **Key Operations:** Build, Query, Update.

---

## Classic Segment Trees

<figure style="text-align: center;">
  <img src="assets/diagrams/segment-tree-diagram.png" alt="Segment Tree Diagram" width="70%">
  <figcaption>Figure 1: Example of a Segment Tree, each box is a node</figcaption>
</figure>

---

### **Building a Segment Tree in C++**

```cpp
#include <iostream>
using namespace std;

// Constants
const int mxn = 2e5 + 5; // Maximum size of the array - 2x10^5 is typically sufficient
int a[mxn];              // Array to hold input values -> values will be arranged in the tree
vector<int> tree;        // Segment tree is represented as a 1D vector (used due to its dynamic nature and ability to change size depending on input)

// Main function to build the segment tree
void build(int id, int le, int ri) { 
    if (le == ri) { // Base case: if the current range is a single element
        tree[id] = a[le]; // Store the corresponding value of a[le] in the tree node
        // This occurs when the bottom row of the above diagram is reached
    } else {
        int mid = (le + ri) / 2; // Calculate the middle value of each interval (range)
        build(id * 2, le, mid);  // Recursively builds the corresponding node on the left subbranch
        // Left subbranches always have an id that is twice the main tree id, with a range from the left value to the mid value.
        build(id * 2 + 1, mid + 1, ri); // Recursively builds the corresponding right subbranches
        // Right subbranches always have an id that is one more than twice the main tree id, with a range from the mid value to the right value.
        tree[id] = tree[id * 2] + tree[id * 2 + 1]; // Builds the current node
        // This occurs for rows between the first row and the last row; each node has a value equivalent to the sum of its two subbranches.
    }
}


## Example Problem
Given an array, find the sum of elements in a range and update values efficiently.

### Code Implementation
```cpp
#include<bits/stdc++.h>
using namespace std;
...

 <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">s
    <!-- Prism.js CSS for syntax highlighting -->
    <link href="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/themes/prism-tomorrow.min.css" rel="stylesheet" />
    
    <!-- Prism.js library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/prism.min.js"></script>
    
    <!-- Prism.js language support for C++ -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/components/prism-cpp.min.js"></script>
</head>

 
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

**Building a Classic Segment Tree in C++**
Function to build a classic segment tree with a range of consecutive values
```cpp
#include <iostream>
using namespace std;

// Constants
const int mxn = 2e5 + 5; // Maximum size of the array - 2x10^5 is typically sufficient
int a[mxn];              // Array to hold input values -> values will be arranged in the tree
vector<int> tree;        // Segment tree is represented as a 1D vector 
// (vector used due to its dynamic nature and ability to change size depending on input)

// Main function to build the segment tree
void build(int id, int le, int ri) { 
  // int le -> represents left most value in segment tree (in above diagram is 0)
  // int ri -> represents right most value in segment tree (in above diagram is 16)
  // int id -> above diagram, nodes are represented as 1:[0,16) -- 1 is the respective id 
    if (le == ri) { // Base case: if the current range is a single element
        tree[id] = a[le]; // Store the value of a[le] in the tree node
        // This occurs when the bottom row of the diagram is reached
    } else {
        int mid = (le + ri) / 2; // Calculate the middle value of the current range

        // Build the left subbranch
        build(id * 2, le, mid);  // Recursively build the left node
        // Left subbranches have id = 2 * parent_id, range: [le, mid]

        // Build the right subbranch
        build(id * 2 + 1, mid + 1, ri); // Recursively build the right node
        // Right subbranches have id = 2 * parent_id + 1, range: [mid+1, ri]

        // Combine the results from the left and right subbranches
        tree[id] = tree[id * 2] + tree[id * 2 + 1]; 
        // Each node value is the sum of its two children
    }
}
```

**Update a Classic Segment Tree in C++**
Function to update/change a value in the segment tree and array
```cpp
void update(int targetPosition, int value, int id, int le, int ri){
    // int targetPosition -> represents index of value to be changed in original array
    // int value -> represents value to be changed to 
    // int id -> id of value to be changed in segment tree
    // int le -> leftmost (least) value represented in segment tree
    // int ri -> rightmost (greatest) value represented in segment tree

    if(le == ri){ // Base case: when the current segment is a single element (leaf node - nodes of bottom row of segment tree diagram)
        a[targetPosition] = value; // Update original array (declared above) with value
        tree[id] = value; // Update current leaf node with value
        return;
        // THIS ONLY HAPPENS WHEN WE REACH A LEAF NODE (BOTTOM ROW) IN SEGMENT TREE
    } else{
        int mid = (le + ri)/2; // calculates the middle index of the current segment 
        if(targetPosition<=mid){
          // if the index to be updated lies in the left half of segment
          update(targetPosition, value, id*2, le, mid); // recursively update left segment and its subbranches (eventually)
          // Left subbranches have id = 2 * parent_id, range: [le, mid]
        } else {
          // otherwise, index to be updated lies in the right half of segment
          update(targetPosition, value, id*2+1, mid+1, ri);// recursively update right segment and its subbranches (eventually)
          // Right subbranches have id = (2 * parent_id) + 1, range: [mid, ri]
        }
        // After calculating branching nodes, recalculate parent/current node
        tree[id] = tree[id * 2] + tree[id * 2 + 1] ;
        // Parent node is update to sum of 2 children nodes
        // Keeps segment tree consistent with all children nodes (taking changes into account)
    }
}
```

## Example Problem
Given an array, find the sum of elements in a range and update values efficiently.

### Code Implementation
```cpp
#include<bits/stdc++.h>
using namespace std;
...

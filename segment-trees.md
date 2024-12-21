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
  //int id refers to the current node, usually starts from 1, representing the entire array
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
    //int id refers to the current node, usually starts from 1, representing the entire array
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
**Query a Classic Segment Tree in C++**
Function that finds the sum of values in a given range, traverses through segment tree to find this
```cpp
int query(int targetLeft, int targetRight, int id, int le, int ri){
    //int targetLeft refers to the leftmost index in array you are interested in summing
    //int targetright refers to the rightmost index in array you are interested in summing
    //int targetright refers to the rightmost index in array you are interested in summing
    //int id refers to the current node, usually starts from 1, representing the entire array
    // int le -> leftmost (least) value represented in segment tree
    // int ri -> rightmost (greatest) value represented in segment tree
    // Base case 1: if current segment is completely outside range
    if(targetLeft > ri || targetRight < le){ 
        return 0; //no overlap with queried range
        //ensures we ignores segments that are out of queried range
    }
    //Base case 2: if current segment is completely inside queried range
    if(targetLeft <= le && ri <= targetRight){ // bases cases
        return tree[id]; // returns value stored at current segment node
        // this node covers the entire range we querying
    }
    //Recursive case: where current segment partially overlaps the queried range, query the left and right children recursively
    int mid = (le+ri)/2;
    // query the left child of current node
    int leftChild = query(targetLeft, targetRight, id*2, le, mid);
     // The left child represents the range [le, mid], so we query this range, and has parent_id *2
    // Query the right child of the current node (right subtree)
    int rightChild = query(targetLeft, targetRight, id * 2 + 1, mid + 1, ri);
    // The right child represents the range [mid+1, ri], so we query this range, id = (parent_id * 2) +1
    //return sum of left and right child recursively for final sum
    return leftChild + rightChild;

}
```


## Example Problem
Given an array of n integers, your task is to process q queries of the following types:

update the value at position k to u
what is the sum of values in range [a,b]?

Input
The first input line has two integers n and q: the number of values and queries.
The second line has n integers x_1,x_2...x_n: the array values.
Finally, there are q lines describing the queries. Each line has three integers: either "1 k u" or "2 a b".
Output
Print the result of each query of type 2.

Example
Input:
8 4
3 2 4 5 1 1 5 3
2 1 4
2 5 6
1 3 1
2 1 4

Output:
14
2
11
### Code Implementation
```cpp
#include <iostream>
#include <vector>
using namespace std;

// Constants
const int mxn = 2e5 + 5;
int a[mxn];                  // Input array
vector<int> tree;            // Segment tree

// Function to build the segment tree
void build(int id, int le, int ri) { 
    if (le == ri) {
        tree[id] = a[le];    // Leaf node stores the array value
    } else {
        int mid = (le + ri) / 2;
        build(id * 2, le, mid);           // Build left subtree
        build(id * 2 + 1, mid + 1, ri);  // Build right subtree
        tree[id] = tree[id * 2] + tree[id * 2 + 1]; // Combine results
    }
}

// Function to update a value in the segment tree
void update(int targetPosition, int value, int id, int le, int ri) {
    if (le == ri) {
        tree[id] = value;    // Update leaf node
    } else {
        int mid = (le + ri) / 2;
        if (targetPosition <= mid) {
            update(targetPosition, value, id * 2, le, mid);       // Update left subtree
        } else {
            update(targetPosition, value, id * 2 + 1, mid + 1, ri); // Update right subtree
        }
        tree[id] = tree[id * 2] + tree[id * 2 + 1]; // Recalculate node value
    }
}

// Function to perform a range sum query
int query(int targetLeft, int targetRight, int id, int le, int ri) {
    if (targetLeft > ri || targetRight < le) {
        return 0; // No overlap
    }
    if (targetLeft <= le && ri <= targetRight) {
        return tree[id]; // Total overlap
    }
    int mid = (le + ri) / 2;
    int leftChild = query(targetLeft, targetRight, id * 2, le, mid);           // Query left subtree
    int rightChild = query(targetLeft, targetRight, id * 2 + 1, mid + 1, ri); // Query right subtree
    return leftChild + rightChild; // Combine results
}

// Main function
int main() {
    int n, q;
    cin >> n >> q;    // Input size of array and number of queries

    for (int i = 0; i < n; i++) {
        cin >> a[i];  // Input array
    }

    tree.resize(4 * n); // Allocate memory for segment tree
    build(1, 0, n - 1); // Build segment tree

    while (q--) {
        int type, x, y;
        cin >> type >> x >> y;
        if (type == 1) {
            // Update query: set position x to value y
            update(x - 1, y, 1, 0, n - 1);
        } else if (type == 2) {
            // Range query: sum of range [x, y]
            cout << query(x - 1, y - 1, 1, 0, n - 1) << endl;
        }
    }
    // we use x-1, n-1 and y-1 because of indexing conventions, as arrays are indexed from 0
    // however, in common language, positions are indexed from 1
    return 0;
}
```

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

 
## Segment Trees

A segment tree is a data structure that stores information about array intervals in its nodes. This allows efficient answering of range queries and updates. 

Complexities(refer to figure below):
Construction:
N + 1/2N + 1/4N + 1/8N +... = O(N) -> efficient
Modification / Updating:
There are log2(N) rows and only one cell in each row is updated so O(log2(n))
Query:
Each sum will only affect log2(N) cells from bottom to top, so O(log2(N))



- **Key Operations:** Build, Query, Update.

---

# Classic Segment Trees
Used for point updates (modifying a single element) and range queries (sum or max over a range)
Update: updates a single element or range directly, recalculating affected parent nodes recursively 
--> makes range updates time inefficient, as all affected nodes in range need to be updated
Query: Answers range queries by combining contributions from relevant child nodes
Basic structure: Each node represents a summary (eg: sum) of a segment of the array

<figure style="text-align: center;">
  <img src="assets/diagrams/segment-tree-diagram.png" alt="Segment Tree Diagram" width="70%">
  <figcaption>Figure 1: Example of a Classic Segment Tree, each box is a node</figcaption>
</figure>

---

**Building a Classic Segment Tree in C++**
Function to build a classic segment tree with a range of consecutive values
<pre><code class="language-cpp">
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
</code></pre>

**Update a Classic Segment Tree in C++**
Function to update/change a value in the segment tree and array
<pre><code class="language-cpp">
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
</code></pre>

**Query a Classic Segment Tree in C++**
Function that finds the sum of values in a given range, traverses through segment tree to find this
<pre><code class="language-cpp">
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
</code></pre>


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
<pre><code class="language-cpp">
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
</code></pre>

# Inverted Segment Trees
Used for range updates and point queries 
Range updates -> incrementing all elements in a range by 1 efficiently
Point query -> find value at position 2 after range updates
--> need to traverse up tree, summing contributions from all relevant nodes

Update: adds or modifies value over a range without fully propagating the changes immediately
--> because updates are just stored at higher-level nodes and are pushed down (lazily applied) only when needed FOR EFFICIENCY
Query: retrieves a value at specific position by aggregating contributions from all ancestors of the position in the tree
Structure: each node represents a "difference" or an "update" that has been applied to its segment

**Build a Inverted Segment Tree**
Same as building a Classic Segment Tree

**Updating an Inverted Segment Tree**
Function modifies values of range [targetleft, targetright] by adding value to all elements in a range(similar code to range query in classic segment tree)
<pre><code class="language-cpp">
void update(int targetLeft, int targetRight, int value, int id, int le, int ri) {
    //int targetLeft refers to the leftmost index in array you are interested in updating
    //int targetright refers to the rightmost index in array you are interested in updating
    //int id refers to the current node, usually starts from 1, representing the entire array
    // int le -> leftmost (least) value represented in segment tree
    // int ri -> rightmost (greatest) value represented in segment tree
    // Base case 1: if current segment is completely outside range
    if (targetLeft > ri || targetRight < le) {
        return; //no overlap with queried range
        //ensures we ignores segments that are out of queried range
    }
    //Base case 2: if current segment is completely inside queried range
    if (targetLeft <= le && ri <= targetRight) {
        tree[id] += value; // Directly update node that covers full range we want to update
        return; 
    }
    //Recursive case: where current segment partially overlaps the queried range, update the left and right children recursively
    int mid = (le + ri) / 2;
    // update the left child of current node
    update(targetLeft, targetRight, value, id * 2, le, mid);
     // The left child represents the range [le, mid], so we update this range, and has parent_id *2
     // Update the right child of the current node (right subtree)
    update(targetLeft, targetRight, value, id * 2 + 1, mid + 1, ri);
    // The right child represents the range [mid+1, ri], so we update this range, id = (parent_id * 2) +1
    //return sum of left and right child recursively for final sum
    return leftChild + rightChild;

    //NOTICE: LOWER MOST ROW IN INVERSE SEGMENT TREE NEVER GETS UPDATED, AS THIS IS INEFFICIENT, INSTEAD ALL HIGHER ANCESTORS ARE UPDATED. THIS REDUCES RANGE UPDATES TO O(LOG N) AS UPDATES AFFECT ONLY THE NODES REQUIRED TO REPRESENT RANGE
}
</code></pre>

**Querying an Inverted Segment Tree**
Point querying retrieves a value at a specific position (denoted by target position). Similar function to range updating in classic segment tree. 

<pre><code class="language-cpp">
int query(int targetPosition, int id, int le, int ri) {
    // int targetPosition -> represents index of value to be queried in original array
    // int value -> represents value to be queried to 
    //int id refers to the current node, usually starts from 1, representing the entire array
    // int le -> leftmost (least) value represented in segment tree
    // int ri -> rightmost (greatest) value represented in segment tree
    if (le == ri) { // Base case: when the current segment is a single element (leaf node - nodes of bottom row of segment tree diagram)
        return tree[id]; // return value in current leafnode

        // THIS ONLY HAPPENS WHEN WE REACH A LEAF NODE (BOTTOM ROW) IN SEGMENT TREE
    }
    int mid = (le + ri) / 2;// calculates the middle index of the current segment 
    int current = tree[id]; // Current node's value
    if (targetPosition <= mid) {
        // if the index to be queried lies in the left half of segment
        current += query(targetPosition, id * 2, le, mid); 
        // recursively query left child and its subbranches (eventually)
        // Left child have id = 2 * parent_id, range: [le, mid]
        
    } else {
        current += query(targetPosition, id * 2 + 1, mid + 1, ri); // recursively query right child and its subbranches (eventually)
        // Right subbranches have id = (2 * parent_id) + 1, range: [mid, ri]
    }
    return current;
    // IN INVERSE TREE STRUCTURE, UNLIKE CLASSIC TREE STRUCTURE, FINAL VALUE IS NOT JUST STORED IN BOTTOM ROW (LEAF NODES), THOSE VALUES ARE ALL TYPICALLY ZERO. THE TRUE VALUE OF THOSE LEAF NODES ARE EQUAL TO THE AGGREGATE VALUE OF ALL OF ITS ANCESTORS. THIS AVOIDS REDUNDANT OPERATIONS, SUCH AS UPDATING ALL BOTTOM ROW VALUES
}

</code></pre>

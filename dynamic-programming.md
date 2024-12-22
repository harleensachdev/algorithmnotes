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

## Dynamic Programming
Dynamic Programming is an algorithmic technique that is an optimization of plain recursion, the idea is to store the results of subproblems so we don't have to recompute them when needed later. Simple optimization can reduce time complexities from exponential to polynomial. 

# Example of Dynamic Programming: Fibonacci Numbers
Consider the problem of finding the nth term in a Fibonacci Sequence

**Brute force approach (NAIVE)**
The time complexity for the below approach is O(2^n) -> extremely slow and inefficient
--> because, everytime you calculate function, the function calls itself twice, for fib(n-1) and fib(n-2)

The auxiliary space (measure of extra memory used for function, excluding input and output) for the below approach is O(n)
--> each function call is placed on a call stack, the depth of the stack depends on how many nested recursive calls are active
--> in this function, stack can grow to n because each call waits for the result of next call (ie when call finally reaches its base case)
``` cpp
// function to find nth fibonacci number
// any fibonacci n number depends on previous 2 numbers
// this apporach repeatedly breaks down problem until reaching base casses
int fib(int n){
    if(n <= 1){ // Base case
        return n; // for indexes 0 and 1, fibonacci sequence element is 1 
    }
    else{
        return fib(n-1) + fib(n-2)
    }
}
int main(){
    int n = 5;
    int result = nthFibonacci(n);
    cout << result << endl;

    return 0;
}
```

**Dynamic Programming Approach 1: Memoization approach**
In the brute force approach, there are many repeated and redundant calculations. A more efficient technique is to store previously computed Fibonacci values in a memo table to avoid repeated calculations. This will ensure that each Fibonacci number is computed only ONCE. 
Time complexity is O(n) as each fibonacci number from 0 to n is only calculated once and stored in map
---> accessing values from map is O(1), so total number of operations is proportional to n, as there are n Fibonacci numbers to compute
Auxiliary space is also O(n) as the memo map stores each n once

```cpp 
// Map stores precomputed fibonacci numbers
// Key: nth term (fibonacci number n)
// Value: Value in fibonacci sequence at position n (determined by key)
map<int, long long> mp; // mp[n] = fib(n)
// Function to compute nth term using memoization algorithm
long long fib memo (int n) {
    // Step 1: check if nth fibonacci number is already computed and stored in map
    if(mp[n]){
        return mp[n];
    } 
    //Step 2: check if it is 0 or 1 term, return n directly as sequence starts with 0 and 1
    if(n <= 1){
        return n;   
    } 
    // Step 3: recursive calculation to find nth fibonacci number 
    // which is equal to n-1 term and n-2 term
    long long ans = fib_memo (n-1) + fib_memo (n-2) ;
    // Store this value in map for future use
    mp [n] = ans;
    return ans;
}

int main() {
    int n = 10; // Change 'n' to compute the nth Fibonacci number
    long long result = fib_memo(n); // Call the memoized Fibonacci function
    
    // Output the result
    cout << "Fibonacci number at position " << n << " is: " << result << endl;

    return 0;
}
```
**Dynamic Programming Approach 2: Bottom up search**
The bottom up approach iteratively builds up the solution by calculating Fibonacci numbers from the bottom up
In the below code, you could use a map/vector to store these previously calculated numbers as so...
``` cpp
    // Create a vector to store Fibonacci numbers
    vector<int> dp(n + 1);

    // Initialize the first two Fibonacci numbers
    dp[0] = 0;
    dp[1] = 1;

    // Fill the vector iteratively
    for (int i = 2; i <= n; ++i){

        // Calculate the next Fibonacci number
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    // Return the nth Fibonacci number
    return dp[n];
```
However, below, I choose a more space optimized approach, where I store values as variables instead.
The time complexity remains as O(n), as the loop always runs from 2 to n, all operations in the loop are O(1)
The auxilary space in the below code is O(1) as integer variables are used to store the current and 2 previous values
--> this is optimized, as if you store all calculated values in a memo vector/map, space is O(n)
``` cpp
#include <iostream>
using namespace std;

int fib_bottomup(int n) {
    // Step 1: Handle edge cases for n = 0 and n = 1
    if (n == 0) 
        return 0; // The 0th Fibonacci number is 0
    if (n == 1) 
        return 1; // The 1st Fibonacci number is 1

    // Step 2: Initialize variables to store the two previous Fibonacci numbers
    int prev2 = 0; // This will store F(n-2), initially F(0)
    int prev1 = 1; // This will store F(n-1), initially F(1)

    // Step 3: Variable to store the current Fibonacci number
    int curr = 0; // Placeholder for F(n)

    // Step 4: Loop from 2 to n to calculate the nth Fibonacci number
    for (int i = 2; i <= n; i++) {
        // Calculate the current Fibonacci number as the sum of the previous two
        curr = prev1 + prev2;

        // Update prev2 to be the value of prev1 (move one step forward)
        prev2 = prev1;

        // Update prev1 to be the value of curr (move one step forward)
        prev1 = curr;
    }

    // Step 5: Return the nth Fibonacci number
    return curr;
}

int main() {
    int n = 10; // Example: Find the 10th Fibonacci number
    cout << "The " << n << "th Fibonacci number is: " << fib_bottomup(n) << endl;
    return 0;
}
```
**Dynamic Programming Approach 3: Matrix exponentiation**
- [Matrix Exponential](matrix-eponential.md)

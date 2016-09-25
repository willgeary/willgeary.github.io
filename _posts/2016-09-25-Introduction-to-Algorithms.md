---
published: true
layout: post
date: '2016-09-25 07:15:14 -0400'
categories: data
title: Introduction to Algorithms
---

CSOR W4246 – Fall, 2016 $\\$

Will Geary (wcg2111)

*Note: All code and pseudocode below uses zero-based indexing.*

# Problem 1
*(10 points) You are given a sorted array A of n integers. The length of the array is n but you
are not given the value of n; an attempt to access element A[i] with i > n returns the error
message ∞. Describe an algorithm that takes an integer x as input and finds a position in the array
containing x, if such a position exists, in $O(log\ n)$ time.* $\\$

We can't use the straightforward binary search algorithm to solve this problem because we don't know the length $n$ of the array to provide an upper boundary to the search. However, it is still possible to find the position $i$ in array $A$ such that $A[i] = x$ in $O(log\ n)$ time because the array is sorted.

The algorithm works as such:

* Start searching from the left side of $A$ at $counter = 0$, then $1, 2, 4, 8, 16, ..., 2^{k}.$ As $k$ gets larger, we will eventually either find $x$ (if we are lucky) **or** we will hit $counter > n$, and the attempt will return an Error and stop. 

    * If we get lucky and $counter == x$, we have found the answer! Return $counter.$
    * Else if $A[counter] < x$, increase the counter from $2^{m}$ to $2^{m+1}$ and try again.
    * Else if an Error is returned, we now know something about $n$, the length of $A$. If an Error is returned at $counter=16$, for example, we know that $8 < n < 16$ because $16$ threw an error and $8$ did not. This is helpful, because we now have an upper bound $counter - 1$ on $A$ and thus we can run binary search to find $x$.

Below is an example of the algorithm in pseudocode.


```python
"""
A = [1, 3, 4 , 5, 8, 9, 10, 13, 14, 17,..., A[n]]
Length of array A is unknown.
Given x, find position i in A such that A[i] = x.
"""

def findPosition(A, x):
    exponent = 0
    counter = 0
    
    # Search as position = 2^0, 2^1, 2^2, ...
    # loop terminates x is found or when
    # counter is greater than length of A
    while NoError:
        if A[counter] == x:
            return counter
        else if A[counter] < x:
            counter = 2**exponent
            exponent += 1
            return step
        else:
            Error
            
    # peform binary search for x in A
    # loop terminates when low boundary
    # closes in on high boundary
    
    high = counter-1
    low = 0

    while low <= high:
        mid = (low + high)/2
        
        # if x is found, return index
        if A[mid] == x:
            return mid
        
        # if x is bigger, update low boundary
        else if A[mid] < x:
            low = mid + 1
        
        # if x is smaller, update high boundary
        else:
            high = mid - 1
```

If this were real code, we would need to use some error handling logic to define $high$. Setting $high = counter - 1$ runs the risk that $high > n$, and that $A[high]$ would throw an error. As explained previously, we know the upper and lower bounds of $high$ and could perform a binary search to find it.

We can prove correctness of this algorithm using induction. $\\$

**1. Base case:** For an empty array $A$ of length $n$ == 0, the algorithm will return Error and stop. The first while loop will return an Error when it attempts to retreive for A[counter], which doesn't exist. The second while loop will never run because $low = 0$ and $high = counter - 1 = 1$, thus $low > high$ and the loop will not inititate.

**2. Induction Hypothesis:** For an array $A$ of unknown length $n$ and given integer $x$, assume the algorithm will return the correct index $i$ such that $A[i] = x$, if $x$ is in fact a member of $A$. 

**3. Induction Step:** Examine the algorithm's success on array $A$ of length $n+1$ and integer $x$. $n$ is unknown, so increasing $n$ by 1 doesn't make any difference. The first while loop searches for length $n+1$ by checking indices 2^0, 2^1, 2^2, 2^3, 2^4,... until an error is thrown. This loop will either find the index of $x$ or find the upper bound of $n+1$. If this loop finds the index of $x$, we are done. If it finds the upper bound of $n+1$, we now have enough information to find the length of $A$ and thus the midpoint of $A$. From here, we conduct binary search on $A$ to find the position of $x$.

The running time $T(n)$ of this algorithm $= O(log\ n)$. The first while loop runs in worst case of $log\ n$ time because it searches $A$ in exponentially-increasing increments (rather than searching each element of $A$ linearly). The second while loop is the binary search algorithm, which also runs in $log\ n$ time. Thus, the algorithm is $O(log\ n)$.

# Problem 2
*(20 points) You are given two sorted lists of sizes m and n. Give an O(log m+log n) algorithm
for computing the k-th item element in the union of the two lists.* $\\$

My ***findKthItem*** algorithm below takes advantage of the fact that both inputs are *sorted* lists. It works like this:

* Because the two lists $M$ and $N$ are sorted, we can find the middle number in both lists in constant time with the calculation $mid= \Bigl\lfloor \cfrac{list.length}{2} \Bigr\rfloor$. 

* Similar to the binary search algorithm, we halve the search area of the lists in each iteration, further investigating either the left or right side of the list depending on whether $k < or > (mid_{M} + mid_{N})$.
    * If $k > (mid_M + mid_N)$ we can ignore the "left half" of the sorted union of the two lists.
        * If $M[mid_M] > N[mid_N]$:
            * Set $N$ to be the right half of itself
            * Set $k$ equal to $k - mid_N - 1$. We reindex $k$ here because we are now searching in $N$ starting at position = $mid_N + 1$ rather than zero. So, we need to decrease $k$ by the corresponding amount, otherwise we would be looking outside of the list.
            * Call the algorithm recursively to continue honing in on the solution
        * Else, $M[mid_M] \leq N[mid_N]$
            * Set $M$ to be the right half of itself
            * Set $k$ equal to $k - mid_M - 1$. We reindex $k$ here because we are now searching in $M$ starting at position = $mid_M + 1$ rather than zero. So, we need to decrease $k$ by the corresponding amount, otherwise we would be looking outside of the list.
            * Call the algorithm recursively to continue honing in on the solution
    * Else, $k$ must be $\leq (mid_M + mid_N)$. Thus, we can ignore the "right half" of the sorted union of the two lists.
        * If $M[mid_M] > N[mid_N]$:
            * Set $M$ to be the left half of itself
            * Call the algorithm recursively to continue honing in on the solution
        * Else if $M[mid_M] \leq N[mid_N]$:
            * Set $N$ to be the left half of itself
            * Call the algorithm recursively to continue honing in on the solution
* The algorithm will continue shrinking the search area of the lists until one of the lists becomes empty. At which point, the $k^{th}$ value of the other, non-empty list will be returned. At this point we have found our solution. $\\$

We can prove correctness of this algorithm using induction. $\\$

**1. Base case:** If either one of the lists has length = 0, the algorithm will correctly return the $k^{th}$ item in the other list.

**2. Induction Hypothesis:** For lists $M$ and $N$ of length $m>0$ and $n>0$ and integer $k$, assume that the algorithm will correctly return the $k^{th}$ item in the union of the two lists. 

**3. Induction Step:** Examine the algorithm's success on lists of length m+1 and n+1. The algorithm calculates $mid_M\ = \lfloor{(m+1)/2}\rfloor$ and $mid_N\ = \lfloor{(n+1)/2}\rfloor$. The $k^{th}$ item can exist in one of four places: The left half of $M$, the right half of $M$, the left half of $N$ or the right half of $N$. The algorithm continues to halve the search area of the union by comparing $k$ to $(mid_M + mid_N)$, updating the active search areas of $M$ or $N$ and then calling itself recursively. By the induction hypothesis, the algorithm will correctly return the $k^{th}$ item in the union of two lists of size $m$ and $n$. Thus, the algorithm will also correctly return the $k^{th}$ item in the union of two lists of size $m+1$ and $n+1$.

The running time $T(n)$ of this algorithm $= O(log\ m + log\ n)$. At each iteration the algorithm halves the active search area of either list. So, the maximum number of levels in the reccurrence tree caused by list $M$ is $log\ m$ and the maximum number of levels in the reccurrence tree caused by list $N$ is $log\ n$. Thus, the maximum total number of levels in the whole reccurence is $log\ m + log\ n$ Each operation in each level runs in constant time, so $T(n) = O(log\ m + log\ n)$. 

Below is an example of the algorithm.


```python
def findKthItem(M, N, k):
    m = len(M)
    n = len(N)
    midM = m // 2
    midN = n // 2
    
    # if one list is empty
    # return kth element of other list
    if m == 0:
        return N[k]
    elif n == 0: 
        return M[k]
    
    # compare k to sum of midpoints
    if k > (midM + midN):
        if M[midM] > N[midN]:
            # reset k
            k = k - midN - 1
            # call function recursively searching
            # the right half of N
            return findKthItem(M, N[midN + 1:], k)
        else:
            # reset k
            k = k - midM - 1
            # call function recursively searching
            # the right half of M
            return findKthItem(M[midM + 1:], N, k)
    else:
        if M[midM] > N[midN]:
            # no need to reset k
            # call function recursively searching
            # the left half of M
            return findKthItem(M[:midM], N, k)
        else:
            # no need to reset k
            # call function recursively searching
            # the left half of N
            return findKthItem(M, N[:midN], k)
```


```python
# Here's an example of the algorithm working on lists M and N

M = [1, 3, 4, 6, 7]
N = [2, 5, 8, 9, 10]

print "findKthItem(M, N, 0) returns: " + str(findKthItem(M, N, 0))
print "findKthItem(M, N, 1) returns: " + str(findKthItem(M, N, 1))
print "findKthItem(M, N, 4) returns: " + str(findKthItem(M, N, 4))
print "findKthItem(M, N, 8) returns: " + str(findKthItem(M, N, 8))
```

    findKthItem(M, N, 0) returns: 1
    findKthItem(M, N, 1) returns: 2
    findKthItem(M, N, 4) returns: 5
    findKthItem(M, N, 8) returns: 9


# Problem 3
*(30 points) Consider an algorithm for integer multiplication of two n-bit numbers where each
number is split into three parts, each with n/3 bits.*

## Part 3 (a)
*(12 points) Similar to the integer multiplication algorithm presented in class, design and explain an algorithm which multiplies the two integers using only six multiplications of n/3-bit numbers (instead of the straightforward nine multiplications).*

Consider two n-bit numbers A and B where each number is split into three parts, each with n/3 bits.

For simplicity, let's examine first the case where $n = 6$.$\\$

**Example with Base 2: ** Let $A = 100110_2.\\$

$\underbrace{100110_2}_{A} = \underbrace{10}_{A_{High}}\ x\ \underbrace{2^{4}}_{2^{\frac{2n}{3}}} + \underbrace{01}_{A_{Mid}}\ x\ \underbrace{2^{2}}_{2^{\frac{n}{3}}} + \underbrace{10}_{A_{Low}} \\$

**Divide and conquer approach: Let $B =$ another six-digit binary number. Solve $A$ x $B$. **$\\$

$A\ x\ B =\underbrace{(A_{High}2^{\frac{2n}{3}} + A_{Mid}2^{\frac{n}{3}} + A_{Low})}_{A}\ ·\ \underbrace{(B_{High}2^{\frac{2n}{3}} + B_{Mid}2^{\frac{n}{3}} + B_{Low})}_{B}$ $\\$

This problem could be solved with nine straightforward multiplications:

*Note: to save space I will use $A_H$ to represent $A_{High}$, etc. from this point forward.*

$A$ x $B = \underbrace{2^{\frac{4n}{3}}A_{H}B_{H}}_{Part\ 1} + \underbrace{2^{n}(A_{H}B_{M} + A_{M}B_{H})}_{Part\ 2} + \underbrace{2^{2n/3}(A_{H}B_{L} + A_{M}B_{M} + A_{L}B_{H})}_{Part\ 3}\dots$

$\ \ \ \ \ \ \ \ \ + \underbrace{2^{\frac{n}{3}}(A_{M}B_{L} + A_{L}B_{M})}_{Part\ 4} + \underbrace{A_{L}B_{L}}_{Part\ 5} \\$

However, it is possible to reduce these nine multiplications down to six multiplications with the added expense of additional addition and substraction operations (like Karatsuba's algorithm, which we discussed in class). $\\$

First, calculate three products: $A_HB_H$, $\ A_MB_M$, $\ A_LB_L.$

Then, use a method similar to Karatsuba's algorithm to replace the middle three parts of the solution with alternate versions, each of which requires only one multiplication (since we already know $A_HB_H$, $\ A_MB_M$, $\ A_LB_L). \\$

$Part\ 2 = 2^n \big[(A_H + A_M)(B_M + B_H) - A_HB_H - A_MB_M\big]\\$

$Part\ 3 = 2^{\frac{2n}{3}} \big[(A_H + A_L)(B_H+B_L) - A_HB_H - A_LB_L + A_MB_M\big]$

$Part\ 4 = 2^{\frac{n}{3}} \big[(A_M + A_L)(B_L + B_M) - A_MB_M - A_LB_L\big]\\$

Thus, the following solution requires only six multiplications of n/3 bit numbers:

$A$ x $B = \underbrace{2^{\frac{4n}{3}}A_{H}B_{H}}_{Part\ 1} + \underbrace{2^n \big[(A_H + A_M)(B_M + B_H) - A_HB_H - A_MB_M\big]}_{Part\ 2}\dots$

$\ \ \ \ \ \ \ \ \ + \underbrace{2^{\frac{2n}{3}} \big[(A_H + A_L)(B_H+B_L) - A_HB_H - A_LB_L + A_MB_M\big]}_{Part\ 3}\dots$

$\ \ \ \ \ \ \ \ \ + \underbrace{2^{\frac{n}{3}} \big[(A_M + A_L)(B_L + B_M) - A_MB_M - A_LB_L\big]}_{Part\ 4} + \underbrace{A_{L}B_{L}}_{Part\ 5}$



We can create an algorithm to recursively solve the six subproblems. Below is an example in pseudocode.


```python
def splitByThree_Multiply(A, B):
    n = len(A)
    
    # Let k be a small constant
    if n == k:
        return A*B
    
    # A_high = first n/3 digits of A, etc.
    A = A_high * 2**(2n/3) + A_mid * 2**(n/3) + A_low
    B = B_high * 2**(2n/3) + B_mid * 2**(n/3) + B_low
    
    # Compute the six products recursively
    AhBh = splitByThree_Multiply(A_high, B_high)
    AmBm = splitByThree_Multiply(A_mid * B_mid)
    AlBl = splitByThree_Multiply(A_low * B_low)
    product4 = splitByThree_Multiply(Ah + Am, Bh + Bm)
    product5 = splitByThree_Multiply(Ah + Al, Bh + Bl)
    product6 = splitByThree_Multiply(Am + Al, Bm + Bl)
    
    # Return product of A x B using formula derived above
    return AhBh * 2**(4n/3) + (product4 - AhBh - AmBm) * 2**n + \
            (product5 - AhBh - AlBl + AmBM) * 2**(2n/3) + \
            (product6 - AmBm - AlBl) * 2**(n/3) + AlBl
```

## Part 3 (b)
*(5 points) Determine the asymptotic running time of your algorithm. Would you rather split it into two parts or three parts?*

The recurrence of my algorithm is $T(n) = 6 T(n/3) + cn.$ By the master theorem, $T(n) = O(n^{log_36}) = O(n^{1.63}).$

I would rather split an array into two parts than three parts. As we saw with Karatsuba's algorithm, when we recursively split an array into two parts: $T(n) \leq 3T(n/2) + cn = O(n^{log_23}) = O(n^{1.59}).$

$n^{1.59} < n^{1.63}.$ So, this is **not** an improvement over Karatsuba. When the multiplier and multiplicand are very big numbers, it is faster to split the array into two parts than it is to split it into three parts with six multiplications.

## Part 3 (c)
*(5 points) Suppose you could use only five multiplications instead of six. What is the
asymptotic running time of such an algorithm? In this case, would you rather split it
into two parts or three parts?*

If you could use only five multiplications instead of six, the running time would be $T(n) = 5T(n/3) + cn.$ By the master theorem, $T(n) = O(n^{log_35}) = O(n^{1.46}).$

$n^{1.46} < n^{1.59}$. So, this **is** an improvement over Karatsuba. When the multiplier and multiplicand are very big numbers, it is faster and preferable to split the array into three parts with five multiplications than it is to split the array into two parts.

## Part 3 (d)
*(8 points) Explain how you could use only five multiplications.*

I could't figure out how to do this on my own. So, I did some research online and found the [Toom-Cook algorithm](https://en.wikipedia.org/wiki/Toom%E2%80%93Cook_multiplication). I assume this is not sufficient for credit on the homework :) but Toom-Cook where k=3 reduces nine multiplications to five. The Karatsuba algorithm is actually a special case of Toom-Cook where k=2 and reduces four multiplcations to three.

# Problem 4
*(20 points) Consider the following recursive algorithm. On input a list of distinct numbers,
the algorithm runs in three phases. In the first phase, the first 2/3 elements of the list are
sorted recursively; when the list has size 2, return the list if ordered, otherwise swap. In the
second phase, the last 2/3 elements are sorted recursively. Finally, in the third phase, the
first 2/3 elements are sorted recursively again. Derive a recurrence describing the running
time of the algorithm and use the recurrence to bound its asymptotic running time. Would
you use this algorithm in your next application?*

This algorithm is somewhat similar to mergesort, which has recurrence $T(n) = 2 T(\frac{n}{2}) + cn$. 

However, here there are three recursive calls in which $\frac{2n}{3}$ elements are sorted in this algorithm. In other words, the algorithm does $O(n)$ work plus three recursive calls on lists of length $\frac{2n}{3}$. So, the recurrence for this algorithm is $T(n) = 3 T(\frac{2n}{3}) + c$.

We can apply the master theorem to this recurrence. The algorithm is $O(n^{log_{3/2}3}) = O(n^{2.71})$. This is very bad! I would **not** use this algorithm in my next application. It is even worse than insertion sort, which is $O(n^{2})$.

# Problem 5
*(30 points) In the table below, indicate the relationship between functions f and g for each
pair (f, g) by writing yes or no in each box. For example, if f = O(g) then write yes in the
first box.*

$f$                | $g$                   | $O$  | $o$ | $\Omega$  | $\omega$ | $\Theta$ 
----------------   | ------------          | ---  | --- | --------  | -------- | -------  
$log^5 n$          | $10\ log^3 n$         | No   | No  | Yes       | Yes      | No   
$n^2 log\ 2n$      | $n\ log\ n$           | No   | No  | Yes       | Yes      | No
$\sqrt{log\ n}$    | $log\ log\ n$         | Yes  | No  | Yes       | No       | Yes
$n^2 + n^{1/3}$    | $n^2 log\ n + n^{5/2}$| Yes  | Yes | No        | No       | No
$\sqrt{n} + 1500$  | $n^{1/3} + log\ n$    | No   | No  | Yes       | Yes      | No
$\frac{3^{n}}{n^2}$| $2^n\ log\ n$         | No   | No  | Yes        | Yes      | No
$n^{log\ n}$       | $2^n$                 | Yes  | Yes | No        | No       | No
$2^n$              | $\frac{3^n}{n^{log\ n}}$| Yes | Yes  | No       | No      | No
$n^n$              | $n!$                  | No   | No  | Yes       | Yes      | No
$log\ n^n$         | $log\ n!$             | Yes  | No  | Yes       | No       | Yes

# Question 3

**You are given a sequence A=(a(1), a(2), ..., a(n)) of length n, where all a(i) are positive integers. Consider all subsequences B of A which satisfy the condition that no two consecutive elements of A, say a(i) and a(i+1), both belong to B. Let sum(B) be the sum of all elements of such a subsequence B. Design a dynamic programming algorithm which finds the largest possible value of sum(B) when B ranges over all subsequences meeting the above condition. In particular, you have to:**
**(a) State clearly the sub-problems that need to be solved**
**(b) write explicitly the recursion equations for solving these sub-problems;**
**(c) explain how the final answer is obtained;**
**(d) estimate the run time of your algorithm.**
**Note that you are only asked to find the maximal possible value of such sums of elements of B satisfying the above condition; you do not have to find B itself, just the sum of its elements.**

Subproblems can be defined where we want to find the subsequence with the largest sum ending on element $A[j]$​​​​​, for each $A[j]$​​​​​ in $A$​​​​​​. We can define this subproblem as $opt(j)$​​​​​, which has the following equation: $opt(j) = A[j] + \max\{opt(i) : 0 \leq i \lt j-1 \}$​​​​​​. ​Assuming indices are zero-indexed, the base case occurs at $opt(0) = A[0]$​​​​​. The solution to the problem is thus the solution of the subproblem with the largest subsequence sum, which can be found by iterating over all the stored subproblem solutions in $O(n)$​. This algorithm has a running time of $O(n)$​​​ since a subproblem is found for each of the $n$​​ elements, where each subproblem references a number of previously solved subproblems (which can be memoized). 





Random:



To solve this problem, we first define the subproblem where we need to find the subsequence of length $k$​​​ with the largest sum. The solution to the problem is thus the subproblem with the largest sum (with various subsequence lengths $k$​​). Each subproblem can be defined by the following recursive equation: $opt(k) = \max_i \{ opt(k-1) + A[i] : A[i-1], A[i+1] \not \in opt(k-1) \}$​​. ​​

Initialise an empty 2D array $S$ where $S[k]$ is an empty list for all $k$. We can iterate over $k$​​ to find the subsequence of length $k$​​ with the largest sum, where no consecutive elements of $A$​​​ occur in the subsequences. We need to keep track of the indices in $A$​​ for elements selected for each subsequence of length $k$, as well as the subsequence sum, as elements in array $S[k]$​​​​​​​. Define the base case where subproblems of length 0 have no possible subsequences. We can add elements to each subsequence of length $k-1$​ where the element's index is $j$, with indices $j-1, j+1$​​ not stored within the subsequence's stored indices. Searching over all $n$ instances of $j-1$ and $j+1$ can each be done in $O(n)$​ time, with a complexity of $O(n^2)$. 



time compelxity



We can store each subsequence in a 2D array $S$​​ where $S[i][j]$​​​ represents the subsequence of length $i$ 







The subsequence of length $k$ with the largest sum is the largest subsequence of length $k-1$ 



At each step, if we include $A[i]$​​, we cannot include $A[i-1]$​ or $A[i+1]$​​ for the remaining subsequence. ​

Subsequence of length $k$

$opt(k) = \max \{ opt(k - 1) + a(i) : a(i-1), a(i+1 ) \not \in B \}$​​​

Doesn't say distinct

Subsequence length

$O(n^2)$


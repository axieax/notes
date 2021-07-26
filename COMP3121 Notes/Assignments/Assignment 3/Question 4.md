# COMP3121 Assignment 3 Question 4

Andrew Xie, z5317337

**You are on vacation for $N$​​​ days at a resort that has three possible activities 1, 2 and 3. For each day $i$​​​​​, for each activity 1, 2 or 3, you've determined how much enjoyment $e(i,j) \gt 0, (1 \leq i \leq n, 1 \leq j \leq 3)$​​​​ you will get out of that activity if you do it on that particular day (the same activity might give you a different amounts of enjoyment at different days). However, you are not allowed to do the same activity two days in a row. Design an algorithm for determining the maximum total enjoyment possible over the stay of $N$​ days and the sequence of activities you should do at each day.**

For this problem, we can define subproblem $E(i,j)$​​​​ as the maximum total enjoyment up to day $1 \leq i \leq n$​​ and doing activity $1 \leq j \leq 3$​​ on day $i$​​​​​​. The solution to the original problem is the maximum of the solutions to subproblems $E(n,j)$​​​ for $1 \leq j \leq 3$​. The subproblem $E(i,j)$ can be defined as the enjoyment received on day $i$ completing activity $j$​​, plus the maximum total enjoyment received up to the previous day by completing the other activities. This can be defined as follows:
$$
E(i,1) = \max \{E(i-1,2), E(i-1,3)\} + e(i,1)\\
E()
$$




```pseudocode
E <- empty hash map of size 3 * N;
for j from 1 to 3, increasing by 1:
	E[(1,j)] <- e[(1,j)];
for i from 2 to n, increasing by 1:
	E[(i,1)] <- max(E[(i-1,2)], E[(i-1,3)]) + e[(i,1)];
	E[(i,2)] <- max(E[(i-1,1)], E[(i-1,3)]) + e[(i,2)];
	E[(i,3)] <- max(E[(i-1,1)], E[(i-1,2)]) + e[(i,3)];
return max(E[(n,1)], E[(n,2)], E[(n,3)]);
```





Going backwards, note max and selected activity

O(N)


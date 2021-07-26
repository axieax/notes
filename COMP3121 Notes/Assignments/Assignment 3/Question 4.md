# COMP3121 Assignment 3 Question 4

Andrew Xie, z5317337

**You are on vacation for $N$​​​ days at a resort that has three possible activities 1, 2 and 3. For each day $i$​​​​​, for each activity 1, 2 or 3, you've determined how much enjoyment $e(i,j) \gt 0, (1 \leq i \leq n, 1 \leq j \leq 3)$​​​​ you will get out of that activity if you do it on that particular day (the same activity might give you a different amounts of enjoyment at different days). However, you are not allowed to do the same activity two days in a row. Design an algorithm for determining the maximum total enjoyment possible over the stay of $N$​ days and the sequence of activities you should do at each day.**

For this problem, we can define subproblem $E(i,j)$​​​​​ as the maximum total enjoyment up to and including day $1 \leq i \leq n$​​​ and doing activity $1 \leq j \leq 3$​​​ on day $i$​​​​​​​. In general, the subproblem $E(i,j)$​ can be defined as the enjoyment received on day $i$​ completing activity $j$​​​​​​, plus the maximum total enjoyment received up to and including the previous day by completing the other activities on that day, i.e.
$$
E(i,1) = \max \{E(i-1,2), E(i-1,3)\} + e(i,1);\\
E(i,2) = \max \{E(i-1,1), E(i-1,3)\} + e(i,2);\\
E(i,3) = \max \{E(i-1,1), E(i-1,2)\} + e(i,3).\\
$$

For each day $i$​​ for each activity $j$​​, we should keep track of which activity on the previous day $i-1$​​ will allow us to obtain the maximum enjoyment for the current day $i$​​, using a predecessor matrix. This allows us to backtrack and find the optimal sequence of activities which should be chosen. As the base case to this recursive definition, the solution to the subproblems for all activities $j$​​ on the first day is just the enjoyment from completing activity $j$​​ on the first day, i.e. $E(1, j) = e(1, j)$​​ for $1 \leq j \leq 3$​​. The solution to the original problem is the maximum of the solutions to subproblems $E(n,j)$​​ for $1 \leq j \leq 3$​​. This algorithm can find the solution in $O(n)$​​ time, iterating over $n$​​ days and evaluating computed subproblems in constant time for each day. We can backtrack to find the sequence of activities which should be taken to achieve this maximum total enjoyment value, in reverse order starting from the activity $j$​​ which allows us to obtain this maximum value. We will then need to traverse through the predecessor matrix on decreasing days to find the best activity which should be taken on the previous days if we choose activity $j$ on the last day and so on, until we reach null. The sequence expected by the problem will be the reverse of the one found through backtracking, and can also be computed in $O(n)$ time since there are $n$​​​​ days in the sequence to be traversed through.

<u>Pseudocode:</u>


```pseudocode
E <- new hash map of size 3 * (N + 1), and all elements with value 0;
pred <- new hash map of size 3 * (N + 1), and all elements with value null;
for i from 1 to n, increasing by 1:
	E[(i,1)] <- max(E[(i-1,2)], E[(i-1,3)]) + e[(i,1)];
	E[(i,2)] <- max(E[(i-1,1)], E[(i-1,3)]) + e[(i,2)];  	
	E[(i,3)] <- max(E[(i-1,1)], E[(i-1,2)]) + e[(i,3)];
	if i >= 2:
		// leave predecessor of activities for day 1 as null
		pred[(i,1)] <- 2 if E[(i-1,2)] > E[(i-1,3)] else 3;
		pred[(i,2)] <- 1 if E[(i-1,1)] > E[(i-1,3)] else 3;
		pred[(i,3)] <- 1 if E[(i-1,1)] > E[(i-1,2)] else 2;
max_total_enjoyment = optimal_last_activity = -1;
for j from 1 to 3, increasing by 1:
	if E[(n,j)] >= max_total_enjoyment:
		max_total_enjoyment <- E[(n,j)];
		optimal_last_activity <- j;
// backtrack to find sequence
seq <- new list of length n;
curr <- optimal_last_activity;
i <- n;
while curr != null;
	Add curr to seq;
	curr = pred[(i, curr)];
	i <- i - 1;
seq.reverse();
```


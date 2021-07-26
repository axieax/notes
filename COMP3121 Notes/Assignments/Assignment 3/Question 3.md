# COMP3121 Assignment 3 Question 3

Andrew Xie, z5317337

**In a pond there is a sequence on $n$ lily pads arranged in a straight line: $1,2,3,...,n$. On lily pad $i$ there are $f_i \geq 0$ flies. On lily pad 1 there is a frog sitting. The frog can only jump forward from a lily pad $i$ to either lily pad $i+3$ or lily pad $i+4$. Find the largest number of flies that the frog can catch.**







Iteratively:



<u>Pseudocode:</u>

```pseudocode
Initialise F with the same dimensions as f, and all elements with value -1;
F[1] = f[1];
FOR k from 4..n (inclusive), increasing by 1:
	best_previous <- max(F[k-3], F[k-4]);
	if best_previous == -1:
		// current lily pad also inaccessible if both lily pads inacessible
		continue;
	else:
		F[k] = best_previous + f[k];
return max(F)
```





Recursively:

Defining a subproblem $F(k)$ as the largest number of flies that the frog can catch, ending on lily pad $k$, the solution to the original problem is the largest solution to all subproblems $F(k)$ for $1 \leq k \leq n$. Since the largest number of flies that the frog can catch up to and including an arbitrary lily pad $k$ depends on the largest number of flies which can be caught up to and including the accessible lily pads $k-3$ and $k-4$, as well as the number of extra flies $f_k \geq 0$ which will be caught by moving from one of these lily pads to lily pad $k$, a recursive function for $F(k)$ can be defined as $F(k) = \max \{F(k-3), F(k-4)\} + f(k)$. Moving from the higher of the subproblems for the accessible lily pads allows us to find the maximum number of flies that the frog can catch if we move from one of those lily pads to get to lily pad $k$. For the base case, lily pad 1 will return $f_i$ since the frog starts on and catches all the flies on that lily pad. Furthermore, lily pads 2 and 3 are inaccessible from lily pad 1, so we can set the solution to the subproblems for these lily pads as -1 to indicate that they are unreachable (since $f_i \geq 0$). It should be noted that as an example, lily pad 6 is also unreachable since lily pads 2 and 3 are both unreachable. As such, the original recursive definition will have to be modified so that if $\max\{F(k-3), F(k-4)\}$ is -1 for any lily pad $k$, then -1 should be returned lily pad $k$ is not accessible. For another example of lily pad 5 which is reachable by four jumps from lily pad 1 only, $\max\{F(2), F(1)\}$ will give $F(1) = f_1$ since lily pad 2 is inaccessible but lily pad 5 can be reached from lily pad 1. 

The recursive definition for $F(6) = \max \{F(3), F(2)\} + f(6)$​ won't work!!

If  is -1, return -1



We can define $F(k)$​​ as a recursive function. Since 

==Need to distinguish between inaccessible lily pads and lily pads without flies==

Go backwards - look forwards 3/4 step find max



O(n)

<u>Pseudocode:</u>

```pseudocode
// add memo
function solve(flies, k):
	if k == 1:
		return flies[1];
	else if k <= 3:
		return -1;
	best_previous <- max(solve(flies, k - 3), solve(flies, k - 4));
	if best_previous == -1:
		return -1;
	else if best_previous != -1:
		return best_previous + flies(k);
memo
return max(memo)
```








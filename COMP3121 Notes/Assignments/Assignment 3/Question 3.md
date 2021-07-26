# COMP3121 Assignment 3 Question 3

Andrew Xie, z5317337

**In a pond there is a sequence on $n$ lily pads arranged in a straight line: $1,2,3,...,n$. On lily pad $i$ there are $f_i \geq 0$ flies. On lily pad 1 there is a frog sitting. The frog can only jump forward from a lily pad $i$ to either lily pad $i+3$ or lily pad $i+4$. Find the largest number of flies that the frog can catch.**

Defining a subproblem $F(k)$​​​​ as the largest number of flies that the frog can catch, ending on lily pad $k$​​​​, the solution to the original problem is the largest solution to all subproblems $F(k)$​​​​ for $1 \leq k \leq n$​​​​. First, initialise a list to store the solutions to all subproblems, with all values -1 representing that the lily pad is unreachable for each subproblem (this value can be updated later). Since the largest number of flies that the frog can catch ending on an arbitrary lily pad $k$​​​​ depends on the largest number of flies which can be caught ending on the immediately accessible lily pads $k-3$​​​​ and $k-4$​​​​, as well as the number of extra flies $f_k \geq 0$​​​​ which will be caught by moving from one of these lily pads to lily pad $k$​​​​, a general recurrence relation for $F(k)$​​​​ can be defined as $F(k) = \max \{F(k-3), F(k-4)\} + f(k)$​​​​. Moving from the lily pad with the higher subproblem value allows us to find the maximum number of flies that the frog can catch if we move from one of these lily pads to get to lily pad $k$​​​​. Designing an iterative algorithm, we can set $F(1)$​​​​ to be $f_1$​​​​ since the frog starts on and catches all the flies on that lily pad. The next two lily pads 2 and 3 are inaccessible since the frog can only jump to lily pad 4 or 5 from lily pad 1. From lily pad 4 onwards, the recurrence relation defined can generally be followed. However, it should be noted that some lily pads $k$​​​​ may also be inaccessible if both lily pads $k-3$​​​​ and $k-4$​​​​ before it are also inaccessible, so $\max \{F(k-3), F(k-4)\} = -1$​​​​ since the subproblems for both lily pads will return -1. Iterating over $k$​​​​, we need to ensure that $\max \{F(k-3), F(k-4)\} \neq -1$​​​​​, or else we can just leave the corresponding subproblem's solution to be -1. ​​It should be noted that when one of the lily pads $k - 3$​​​ or $k-4$​​​​​ is inaccessible, lily pad $k$​​​ can still be accessed from the other lily pad, whose subproblem solution will be taken by the maximum function since its value will be greater than or equal to zero (due to $f_i \geq 0$​​​ for all $1 \leq i \leq n$​​​​​​​), while the subproblem solution for the inaccessible lily pad will be -1.

After iterating over $k$​​ from 4 to $n$​​ in linear $O(n)$​​​ time​​, where each iteration involves constant time evaluations of already computed subproblems, the solution to the problem can be obtained by finding the maximum value in the list of $n$​​ saved subproblem solutions, also in $O(n)$​ time. Hence, this algorithm will find the largest number of flies that the frog can catch in $O(n)$​ time. 

<u>Pseudocode:</u>

```pseudocode
Initialise F with the same dimensions as f, and all elements with value -1;
F[1] = f[1];
for k from 4..n (inclusive), increasing by 1:
	best_previous <- max(F[k-3], F[k-4]);
	if best_previous != -1:
		F[k] = best_previous + f[k];
return max(F)
```


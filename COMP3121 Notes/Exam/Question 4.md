# Question 4

**You are given a rooted tree T with n vertices. Each vertex is coloured either blue or green.** 

**(a) Your first task is to design a dynamic programming algorithm which for every sub-tree T(i), rooted at a vertex i of T, finds the LENGTH of the longest possible path through the sub-tree T(i) which starts at the root i and such that all vertices on this path are of the same colour as the root i. Your algorithm MUST run in time O(n). (8 points)**

**(b) Your second task is to design a dynamic programming algorithm which finds the LENGTH of the longest possible path  through the tree (not necessarily starting at the root) such that all vertices on this path are of the same colour, either all blue or all green. (12 points)**

**In particular, for both problems separately, you have to:**
**(a) state clearly what the sub-problems are that need to be solved;**
**(b) describe precisely the recursion for solving these sub-problems;**
**(c) explain how the final answer is obtained;**
**(d) estimate the run time of your algorithm.**

**Note that you are only asked to find the length of such a path and not the path itself.**
**Also, you can use the solution of problem (a) to help you obtain a solution of problem (b).** 

## Problem a

Problem a can be solved by defining subproblems where we want to find the length of the longest possible path for a subtree $T(u)$​​​​​​​​ with the same colour as its root $u$​​​​​​​​​. Recursively, these subproblems can be defined as $opt(u,c) = 1 + \max_v \{opt(v,c)\}$​​​​​​​​​​​ where $v$​​​ represents a child node of $u$​​​ with the same colour $c$​​​​. We only need to consider vertices $v$​​ with the same colour as $u$​​ as the question suggests. The base case can be defined when a node $u$​​ has no child vertices $v$​​​​​​​, in which case return 1. This final solution is thus the subproblem which is rooted at vertex $i$, i.e. $opt(i,colour(i))$ where $colour(i)$ represents the colour of vertex $i$. This subproblem can be found in $O(n)$ time since we are traversing each of its child vertices, at most $n$ times in total. 

## Problem b

Problem b can be solved by defining subproblems where we want to find the longest same-colour path which ends on the vertex $v$​. Recursively, subproblem can be defined as $opt(v, c) = opt(u,c)+1$​ where vertex $u$​ represents an incoming vertex to $v$​ with the same colour $c$​, or 0 otherise if the $u$ and $v$ don't share a common colour. If the colours are different, we should not consider this path since no subtree can be resolved connected to this node. We can use breadth-first search (BFS) to iterate over each of the $n$ vertices and $n$ edges in $O(V+E) = O(n)$ time. The base case is defined at the root node $t$ of $T$, where $opt(t,c) = 1$ if $t$ is of colour $c$, otherwise 0. Each of the connected vertices $v$ will thus have a subproblem solution of $opt(v,c)$ if $v$ shares a common colour with the incoming vertex $u$, otherwise 0 again. We can store each of these subproblem solutions to an array, and find the highest such subproblem solution in $O(n)$ time to obtain the final solution for problem b. This algorithm has a total complexity of $O(n)$​​. 


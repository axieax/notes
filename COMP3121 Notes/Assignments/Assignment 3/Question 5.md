# COMP3121 Assignment 3 Question 5

Andrew Xie, z5317337

**Given a weighted directed graph $G(V,E)$, find a path in $G$ (possible self-intersecting) of length exactly $K$ that has the maximum total weight. The path can visit a vertex multiple times and can traverse an edge also multiple times. It can also start and end at arbitrary vertices or even start and end at the same vertex.**

Let $W(i,j)$​​​​​​ represent the weight of the edge connecting $i$​​​​​​ to $j$​​​​​​. To solve this problem, first define the subproblem $F(j,k)$​​​​​​ as the maximum total weight for a path of length $0 \leq k \leq K$​​​​​​ ending at node $1 \leq j \leq |V|$​​​​​​. It should be noted that the solution to the subproblem $F(j,k)$​​​​​​ can be found by examining all incoming edges from node $i$​​​​​​ to node $j$​​​​​​, and taking the maximum of each connection's weight $W(i,j)$​​​​​​ plus the maximum total weight of the connecting node $i$​​​​​​ for a path of length $k-1$​​​​​​​. The general recurrence relation can be defined as $F(j,k) = \max\{F(i,k-1) + W(i,j) : (i,j) \in E \}$​​​​​​​. For the algorithm, initialise a hash map of size $(K+1)|V|$​​​ to store the solutions to all subproblems $F(j,k)$​​ for all $1 \leq j \leq |V|$​​ and $0 \leq k \leq K$​. Our base case is $F(j,0) = 0$ for all $1 \leq j \leq |V|$​​, since no weights have been traversed in a path of length 0. We then iterate over $k$ from 1 to K, where in each iteration, we will need to update and store the solutions to the subproblems $F(j,k)$ for all $1 \leq j \leq |V|$​, using the recurrence relation described above. ​​We also need to store the node $i$ corresponding to the highest $F(i,k-1) + W(i,j)$​​ for each subproblem in a predecessor matrix, which we can traverse over to obtain the optimal path of length $K$. 

V+E to another data structure

Base case: F(j, 0) = max(W())

Updating

$O(k|E| + |V|)$​​

```pseudocode
F <- new hash map of size (K + 1) * |V|, and all elements with value 0;
pred <- new hash map of size K * |V|, and all elements value null;
for k from 1 to K, increasing by 1:
	for i, j, W[(i,j)] in E:
		update <- F[(i,k-1)] + W[(i,j)];
		if update > F[(j,k)]:
			F[(j,k)] <- update;
			pred[(j,k)] <- i;
// backtrack
optimal_end_node = optimal_end_weight = -1;
for j in 1 to |V|, increasing by 1:
	if F[(j,K)] > optimal_end_weight:
		optimal_end_weight <- F[(j,K)];
		optimal_end_node <- j;
path <- new list of length k;
curr <- optimal_end_node;
while curr != null:
	path.add(curr);
path.reverse();
```

FIND PATH

Save pred

Keep updating best to each node

Backtrack

Explore all paths of length exactly K, count total weight in path and update best

BFS?

Traverse backjwards?

O(K(V+E))


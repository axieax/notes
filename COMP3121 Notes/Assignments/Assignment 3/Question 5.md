# COMP3121 Assignment 3 Question 5

Andrew Xie, z5317337

**Given a weighted directed graph $G(V,E)$, find a path in $G$ (possible self-intersecting) of length exactly $K$ that has the maximum total weight. The path can visit a vertex multiple times and can traverse an edge also multiple times. It can also start and end at arbitrary vertices or even start and end at the same vertex.**

Let $W(i,j)$​​​​ represent the weight of the edge connecting $i$​​​​ to $j$​​​​. To solve this problem, first define the subproblem $F(j,k)$​​​​ as the maximum total weight for a path of length $0 \leq k \leq K$​​​​ ending at node $1 \leq j \leq |V|$​​​​. It should be noted that the solution to the subproblem $F(j,k)$​​​​ can be found by examining all incoming edges from node $i$​​​​ to node $j$​​​​, and taking the maximum of each connection's weight $W(i,j)$​​​​ plus the maximum total weight of the connecting node $i$​​​​ for a path of length $k-1$​​​​. This can generally be described by the recurrence relation $F(j,k) = \max\{F(i,k-1) + W(i,j) : (i,j) \in E \}$​​​​. For the algorithm, initialise a hash map of size $(K+1)|V|$​​​​ to store the solutions to all subproblems $F(j,k)$​​​​ for all $1 \leq j \leq |V|$​​​​ and $0 \leq k \leq K$​​​​. Our base case is $F(j,0) = 0$​​​​ for all $1 \leq j \leq |V|$​​​​, since no weights have been traversed in a path of length 0, so we can initialise all the values in the hash map to be 0. We then iterate over $k$​​​​ from 1 to K, where in each iteration, we will need to update and store the solutions to the subproblems $F(j,k)$​​​​ for all $1 \leq j \leq |V|$​​​​ using the recurrence relation described above. Since we want to take the maximum of each $F(i,k-1) + W(i,j)$​​​​ for each node $j$​​​​, we can iterate through all the edges in $O(|E|)$​​​​ or $O(|V|^2)$​​​​ time, updating each $F(j,k)$​​​​ if the value for $F(i,k-1) + W(i,j)$​​​​ is greater than what we currently have for $F(j,k)$​​​​. We also need to store the value $i$​​​ which allows us to obtain the highest $F(j,k)$​​​ for each node $j$​​​​, in a predecessor matrix with the same dimensions as the hash map for storing solutions to the subproblems, initialising all elements with the value null. After iterating through all the $|E|$​​​ edges, the maximum value of $F(j,k)$ will be found for each $j$. Computing all these subproblems thus takes $O(K|E|)$​​​​​ time. 

We will now need to iterate over all $1 \leq j \leq |V|$​ nodes to find the maximum of all subproblems $F(j,K)$​​. We can use a similar technique from before to find the $j$​​​ which corresponds to the greatest of these values. We will now need to traverse through the predecessor matrix with decreasing path length $k$​ in order to obtain the original path, starting at this optimal end node $j$​​​​​​ and ending when we reach null. Reversing this path yields the solution to the original problem. Backtracking to find the path takes $O(|V| + K)$​ time, so the entire algorithm takes $O(K|E| + |V|)$​​ time. ​

```pseudocode
F <- new hash map of size (K + 1) * |V|, and all elements with value 0;
pred <- new hash map of size (K + 1) * |V|, and all elements value null;
// compute subproblems
for k from 1 to K, increasing by 1:
	for i, j, W[(i,j)] in E:
		update <- F[(i,k-1)] + W[(i,j)];
		if update >= F[(j,k)]:
			F[(j,k)] <- update;
			pred[(j,k)] <- i;
// backtrack to find optimal path
optimal_end_node = optimal_end_weight = -1;
for j in 1 to |V|, increasing by 1:
	if F[(j,K)] >= optimal_end_weight:
		optimal_end_weight <- F[(j,K)];
		optimal_end_node <- j;
path <- new list of length K;
curr <- optimal_end_node;
k <- K;
while curr != null:
	path.add(curr);
	curr <- pred[(curr, k)]
	k <- k - 1;
path.reverse();
```


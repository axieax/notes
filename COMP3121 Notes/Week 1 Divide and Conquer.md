Breaking down into subproblems

$m$ users ranking same set of $n$ movies. Measure similarity between two users $A$ and $B$. 

Good measure would be to see how different these two users are by counting the total number of inversions, i.e. total number of pairs of movies $i,j$ such that movie $i$ precedes movie $j$ on B's list but movie $j$ is higher up on A's list than the movie $i$. 

Instead of comparing each unique pair $i \lt j$ in $O(n^2)$​, use divide and conquer in $O(n \log n)$.​


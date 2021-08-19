# Question 2

**A sub-graph H of a graph G consists of a subset of vertices of G together with all edges of G whose both ends are in H. Assume now that you are given an undirected graph G with N vertices by an adjacency matrix (i.e., a matrix M such that M(i, j)=1 if there is an edge with end points i and j and M(i, j)=0 otherwise). Design an algorithm which finds a largest size sub-graph H of G such that every vertex of H is adjacent (i.e., connected by an edge) with at least 3 other vertices of H and is also NOT adjacent with at least 3 other vertices of H; if there is no such sub-graph the algorithm should output a message “No such H can be found”. Estimate the run time of your algorithm as a function of the number of vertices N.**

Since we are referencing an adjacency matrix representation of an undirected graph $G$​, the nodes connected to a vertex $u$​ are vertices $v$​ where $M(u,v)=1$​. Store each vertex $u$​ along with this list of vertices $v$​ into an array, with elements $(u, list(v))$​​. Remove elements from this array if the length of list(v) < 3, and save these to a separate array. We then update the initial array with the removed elements, removing them from each list(v). This occurs in $O(N^2)$​ time. Afterwards, remove vertices which are not adjacent with at least 3 other vertices, and update the list once again with removed vertices in $O(N^2)$​ time. We can use the greedy method to find the largest subgraph $H$​ by sorting in $O(N \log N)$​ and choosing the highest vertices. No solution is found if no such subgraphs can be found.



 by the number of such connected vertices $v$​​​ (second element) from highest to lowest​​​. Iterating over this array from the end to the beginning, remove 

Disconnect vertices who are not connected to at least three other vertices. 

Sort?

Union find


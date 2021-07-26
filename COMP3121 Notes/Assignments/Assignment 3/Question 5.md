# COMP3121 Assignment 3 Question 5

Andrew Xie, z5317337

**Given a weighted directed graph $G(V,E)$, find a path in $G$ (possible self-intersecting) of length exactly $K$ that has the maximum total weight. The path can visit a vertex multiple times and can traverse an edge also multiple times. It can also start and end at arbitrary vertices or even start and end at the same vertex.**

This problem can be expressed 



Keep updating best to each node

Backtrack

Explore all paths of length exactly K, count total weight in path and update best

BFS?

Traverse backjwards?

O(K(V+E))

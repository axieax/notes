# COMP3121 Assignment 4 Question 2

Andrew Xie, z5317337

**You have $n$ warehouses and $n$ shops. At each warehouse, a truck is loaded with enough goods to supply one shop. There are $m$ roads, each going from a warehouse to a shop, and driving along the $i$th road takes $d_i$ hours, where $d_i$​ is an integer. Design a polynomial time algorithm to send the trucks to the shops, minimising the time until all shops are supplied.**

Since roads go from $n$​​ warehouses to $n$​​ shops, this relationship can be modelled using a bipartite directed graph, with edges representing $m$​​ roads going from the set of warehouse vertices to the set of shop vertices. We can extend this graph as a flow network, defining a super source node with outgoing edges connected to all warehouses (sources), as well as a super sink node with incoming edges connected from all shops (sinks). Setting the capacity of all incoming edges to the super sink as 1, we can check that all shops have been supplied if the maximum flow from the network is $n$​​. Since all integers $d_i$​​ are positive integers, this capacity of 1 becomes a bottleneck for all augmented paths in the network, so we can similarly set all edges to have a capacity of 1. The Ford-Fulkerson or Edmonds-Karp algorithm can be used to find the maximum flow in the network in $O(|V||E|^2) = O((2n+2)\cdot(m+2n)^2) = O(n(m+n)^2)$​​​ time

Roads less than or equal to selected g_i. 

Since roads can only go from one warehouse to one shop, 

Similarly, we can this is bottleneck

Since we want all shops to be supplied (with flow through each shop), the incoming edges to the super sink should have a capacity of 1.

All edges should have a capacity of 1 since 

These edges should have a capacity of 1

all capacity 1

define nodes

Since we want flow across all $n$ shops

Since we want all $n$​​​ shops to be covered, 

we can model 

reachable



Minimise highest $d_i$​ since assumed simultaneous

Combine a binary search with a max flow. By sorting you can assume that $d_i$​ form an increasing sequence. Fix $i$​ and consider only roads which that $\leq d_i$​ hours to travel from a warehouse to the corresponding shop and use max flow to see if they are enough to obtain a matching of warehouses with shops which is of size $n$​. Use a binary search on $i$​ to find the smallest $d_i$​ which meets the requirements.

All at the same time - minimise the maximum

Use binary search to find the smallest maximum such that all $n$ shops can still be supplied

1 as bottleneck capacity?


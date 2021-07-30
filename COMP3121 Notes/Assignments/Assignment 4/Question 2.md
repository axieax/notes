# COMP3121 Assignment 4 Question 2

Andrew Xie, z5317337

**You have $n$ warehouses and $n$ shops. At each warehouse, a truck is loaded with enough goods to supply one shop. There are $m$ roads, each going from a warehouse to a shop, and driving along the $i$th road takes $d_i$ hours, where $d_i$​ is an integer. Design a polynomial time algorithm to send the trucks to the shops, minimising the time until all shops are supplied.**

Combine a binary search with a max flow. By sorting you can assume that $d_i$ form an increasing sequence. Fix $i$ and consider only roads which that $\leq d_i$ hours to travel from a warehouse to the corresponding shop and use max flow to see if they are enough to obtain a matching of warehouses with shops which is of size $n$. Use a binary search on $i$ to find the smallest $d_i$ which meets the requirements.


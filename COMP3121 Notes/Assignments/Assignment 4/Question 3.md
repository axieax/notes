# COMP3121 Assignment 4 Question 3

Andrew Xie, z5317337

**A large group of children has assembled to play a modified version of hop-scotch. The squares appear in a line, numbered from 0 to $n$, and a child is successful if they start at square 0 and make a sequence of jumps to reach square $n$. However, each child can only jump at most $k \lt n$ squares at a time, i.e., from square $i$ they can reach squares $i+1,i+2,...,i+k$ but not $i+k+1$, and a child cannot clear the entire game in one jump. An addittional rule of the game specifies an array $A[1,...,n-1]$, where $A[i]$ is the maximum number of children who can jump on square $i$​. Assuming the children cooperate, what is the largest number of children who can successfully complete the game?**

Max number of children who can jump to or from square i?

Connect every square $i$ with squares $i+1,...,i+k$ with a directed edge of infinite capacity. Now limit the capacity of each square appropriately and use max flow.


# COMP3121 Assignment 3 Question 2

Andrew Xie, z5317337

**You are given a 2D map consisting of a $C \times R$ grid of squares; in each square there is a number representing the elevation of the terrain at that square. Find a path going from square $(1,R)$ which is the top left corner of the map to square $(C,1)$​ in the lower right corner which from every square goes only to the square immediately below or to the square immediately to the right so that the number of moves from lower elevation to higher elevation along such a path is as small as possible.** 

For this problem it can be observed that the minimum number of moves from lower elevation to higher elevation from square $(1,R)$​ to a square $(x,y)$​ is given by the minimum between the two ways of getting to square $(x,y)$:

1. If the above square exists, the minimum number of such moves from square $(1,R)$​​ to the above square $(x,y+1)$​, plus the number of such moves from square $(x,y+1)$​​ to $(x,y)$​​​.
2. If the left square exists, the minimum number of such moves from square $(1,R)$​​ to the left square $(x-1,y)$,​​ plus the number of such moves from square $(x-1,y)$​​ to $(x,y)$​​​.

We can define a recursive algorithm for finding the minimum number of moves, incorporating memoization to save repeated calculations as well as allow us to backtrack and find the shortest such path. Here, I am also assuming that the number of moves from the question is referring to the total number of times along the path where there is an increase in elevation between squares, rather than the total number of elevation increases along the path. As mentioned above, the algorithm for calculating the minimum of such moves to $(x,y)$ would involve finding the minimum of such moves to the square immediately above and to the left of $(x,y)$ if they exist. If one of them does not exist (i.e. square $(x,y)$ is located amongst the top or left edge of the grid), set them to be $\infty$ since we cannot consider a path from that direction anyways (so that the minimum function considering both directions will avoid this direction). For left or above squares that exist, we will need to recursively find the minimum number of such moves from square $(1,R)$ to that square as a subproblem. If we were to get to square $(x,y)$ from that previous left or above square, the minimum number of such moves from $(1,R)$ to $(x,y)$ would involve an additional cost if moving from that square to $(x,y)$ means moving from lower elevation to higher elevation. This additional cost will need to be factored into whether it is more optimal to reach square $(x,y)$ from the square on the left, or the square above, which can be solved by taking the minimum of such costs. For each of such comparisons, we should also store the more optimal previous square chosen at this step into a predecessor matrix (left or above square which would involve a smaller cost). Assuming paths begin on square $(1,R)$, the base case is the number of such moves from square $(1,R)$ to square $(1,R)$, which would be zero since there is no elevation difference. Notably, this is also the only square with no squares to its left and above it.

Having found the minimum number of moves from lower elevation to higher elevation, from square $(1,R)$​​​ to each square, as well as the best immediate square (left or above) to get to each square, we can traverse through the chain of previous squares from $(C,1)$​​​ to $(1,R)$​​​ and find the path in reversed order. Reversing this path will allow us to answer the problem and find a path with the minimum number of such moves from $(1,R)$​​​ to $(C,1)$​​​. Calculating the minimum number of such moves for each of the $R \times C$​​​ squares will take $O(RC)$​​​​​ since​ comparisons between no more than two already computed subproblems occur in constant time for each square. Furthermore, backtracking through the complete path consisting of $R \times C - 1$​​​​ squares using the predecessor matrix also takes constant time for each square. Hence, the time complexity of this algorithm is $O(RC)$​​​. ​

<u>Pseudocode:</u>

```pseudocode
Initialise memo with the same dimensions as grid, and all squares with value -1;
Initialise pred with the same dimensions as grid, and all squares with value null;

function min_moves(grid, memo, pred, target_x, target_y):
	// memoization
	if memo[(target_x, target_y)] != -1:
		// return min_moves to square if already calculated
		return memo[(target_x, target_y)]
	// base case - top left square
	if target_x == target_y == 1:
		memo[(1,1)] <- 0;
		return 0;
	// recursive case
	current_elevation <- grid[(target_x, target_y)];
	min_from_above = min_from_left = INT_MAX;
	// if above square exists
	if target_y + 1 <= R:
		// calculate min moves to above square
		min_above_moves <- min_moves(grid, memo, pred, target_x, target_y + 1);
		elevation_above = grid[(target_x, target_y + 1)];
		min_from_above <- min_above_moves + (current_elevation > elevation_above);
	// if left square exists
	if target_x - 1 >= 1:
		// calculate min moves to left square
		min_left_moves <- min_moves(grid, memo, pred, target_x - 1, target_y);
		elevation_left = grid[(target_x - 1, target_y)];
		min_from_left <- min_left_moves + (current_elevation > elevation_left);
	// find min moves to current target
	if min_from_above < min_from_left:
		previous_square <- (target_x, target_y + 1);
		min_moves_current <- min_from_above;
	else if min_from_left < min_from_above:
		previous_square <- (target_x - 1, target_y);
		min_moves_current <- min_from_left;
	memo[(target_x, target_y)] <- min_moves_current;
	pred[(target_x, target_y)] <- previous_square;
	return min_moves_current;

// find the minimum of such moves from (1,R) to (C,1)
path_cost <- min_moves(grid, memo, pred, C, 1);

// backtrack to find path
path <- empty list;
curr <- (C,1);
while curr != null:
	previous_square <- pred[curr];
	Add previous_square to path;
	curr <- previous_square;
path.reverse();
```



# COMP3121 Assignment 3 Question 2

Andrew Xie, z5317337

**You are given a 2D map consisting of a C x R grid of squares; in each square there is a number representing the elevation of the terrain at that square. Find a path going from square (1,R) which is the top left corner of the map to square (C,1) in the lower right corner which from every square goes only to the square immediately below or to the square immediately to the right so that the number of moves from lower elevation to higher elevation along such a path is as small as possible.** 

For this problem it can be observed that the minimum number of moves from lower elevation to higher elevation from square (1,R) to a square (x,y) is given by the minimum between the two ways of getting to square (x,y):

1. The minimum number of such moves from square (1,R) to the above square (x,y+1) plus the number of such moves from square (x,y+1) to (x,y), if the above square exists
2. The minimum number of such moves from square (1,R) to the left square (x-1,y) plus the number of such moves from square (x-1,y) to (x,y), if the left square exists.

We can define a recursive algorithm for finding the minimum number of moves, incorporating memoization to save repeated calculations as well as allow us to backtrack and find the shortest such path. As mentioned above, the algorithm for calculating the minimum of such moves to (x,y) would involve finding the minimum of such moves to the square immediately above and to the left of (x,y) if they exist. If one of them does not exist (i.e. square (x,y) is located amongst the top or left edge of the grid), set them to be $\infty$​​ since we cannot consider a path from that direction anyways (so that the minimum function considering both directions will avoid this direction). For left or above squares that exist, we will need to recursively find the minimum number of such moves from square (1,R) to that square. If we were to get to square (x,y) from that previous left or above square, the minimum number of such moves from (1,R) to (x,y) would involve an additional cost if moving from that square to (x,y) means moving from lower elevation to higher elevation. This additional cost will need to be factored into whether it is more optimal to reach square (x,y) from the square on the left, or the square above, which can be solved by taking the minimum of such costs. Assuming paths begin on square (1,R), the base case is the number of such moves from square (1,R) to square (1,R), which would be zero since there is no elevation difference.

The path 

Store which we chosen from smth to smth - and traverse positions





Don't need to go backwards and recompute - just follow chain from (1,R) - what if same - better to recompute

Backtracking to find the path traversed to obtain the minimum number of such moves to square (C,1), we can use the fact that we selected the 

Add elevation diff?

Complexity

==1 instead of 0==



<u>Pseudocode:</u>

```pseudocode
Initialise memo with the same dimensions as grid, and all squares -1;

function min_moves(grid, memo, target_x, target_y):
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
		min_above_moves <- min_moves(grid, memo, target_x, target_y + 1);
		elevation_above = grid[(target_x, target_y + 1)];
		elevation_diff = current_elevation - elevation_above;
		min_from_above <- min_above_moves + (elevation_diff if elevation_diff > 0 else 0);
	// if left square exists
	if target_x - 1 >= 1:
		// calculate min moves to left square
		min_left_moves <- min_moves(grid, memo, target_x - 1, target_y);
		elevation_left = grid[(target_x - 1, target_y)];
		elevation_diff = current_elevation - elevation_left;
		min_from_left <- min_left_moves + (elevation_diff if elevation_diff > 0 else 0);
	// find min moves to current target
	min_moves_current <- min(min_from_above, min_from_left);
	memo[(target_x, target_y)] <- min_moves_current;
	return min_moves_current;

// backtrack returns REVERSED path
path <- empty list;
function backtrack(memo, target_x, target_y):
	Add (target_x, target_y) to path;
	if target_x == target_y == 1:
		// reached start
		return
	above = left = INT_MAX;
	if target_y + 1 <= R:
		above <- memo[(target_x, target_y + 1)];
	if target_x - 1 >= 1:
		left <- memo[(target_x - 1, target_y)];
	if left < above:
		// go left
		backtrack(memo, target_x - 1, target_y);
	else:
		// go up
		backtrack(memo, target_x, target_y + 1);

path.reverse();
```






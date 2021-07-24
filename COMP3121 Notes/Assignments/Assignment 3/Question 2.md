# COMP3121 Assignment 3 Question 2

Andrew Xie, z5317337

**You are given a 2D map consisting of a C x R grid of squares; in each square there is a number representing the elevation of the terrain at that square. Find a path going from square (1,R) which is the top left corner of the map to square (C,1) in the lower right corner which from every square goes only to the square immediately below or to the square immediately to the right so that the number of moves from lower elevation to higher elevation along such a path is as small as possible.** 

For this problem it can be observed that the minimum number of moves from lower elevation to higher elevation from square (C,1) to a square (x,y) is given by the minimum between the two ways of getting to square (x,y):

1. The minimum number of such moves to the square above (x,y+1) plus the number of such moves from square (x,y+1) to (x,y)
2. The minimum number of such moves to the square on the left (x-1,y) plus the number of such moves from square (x-1,y) to (x,y).





<u>Pseudocode:</u>

```pseudocode
Initialise memo with the same dimensions as grid, and all squares -1;

function min_moves(grid, memo, target_x, target_y):
	// memoization
	if memo[target_x][target_y] != -1:
		return memo[target_x][target_y]
	// base case
	if target_x == target_y == 0:
		memo[0][0] <- 0;
		return 0;
	// recursive case
	current_elevation <- grid[target_x][target_y];
	min_from_above = min_from_left = INT_MAX;
	// if can go up
	if target_y + 1 <= R:
		min_above_moves <- min_moves(grid, memo, target_x, target_y + 1);
		elevation_above = grid[target_x][target_y + 1];
		elevation_diff = current_elevation - elevation_above;
		min_from_above <- min_above_moves + (elevation_diff if elevation_diff > 0 else 0);
	// if can go left
	if target_x - 1 >= 0:
		min_left_moves <- min_moves(grid, memo, target_x - 1, target_y);
		elevation_left = grid[target_x - 1][target_y];
		elevation_diff = current_elevation - elevation_left;
		min_from_left <- min_left_moves + (elevation_diff if elevation_diff > 0 else 0);
	// find min moves to current target
	min_moves_current <- min(min_from_above, min_from_left);
	memo[target_x][target_y] <- min_moves_current;
	return min_moves_current;

// backtrack returns REVERSED path
path <- empty list;
function backtrack(memo, target_x, target_y):
	Add (target_x, target_y) to path;
	if target_x == target_y == 0:
		// reached start
		return
	above = left = INT_MAX;
	if target_y + 1 <= R:
		above <- memo[target_x][target_y + 1];
	if target_x - 1 >= 0:
		left <- memo[target_x - 1][target_y];
	if left < above:
		// go left
		backtrack(memo, target_x - 1, target_y);
	else:
		// go up
		backtrack(memo, target_x, target_y + 1);

path.reverse();
```






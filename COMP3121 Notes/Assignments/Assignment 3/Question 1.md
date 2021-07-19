# COMP3121 Assignment 3 Question 1

Andrew Xie, z5317337

Question



Split each operator into LHS and RHS - see how many ways each side can evaluate to true

```pseudocode
// For each OPERATION in the truth table, each truthLookup tuple represents (LHS_truth, RHS_truth, LHS_truth OPERATION RHS_truth)
truthTable = {
	'AND': [(true, true, true), (true, false, false), (false, true, false), (false, false, false)],
	'OR': [(true, true, true), (true, false, true), (false, true, true), (false, false, false)],
	'NAND': [(true, true, false), (true, false, true), (false, true, true), (false, false, true)],
	'NOR': [(true, true, false), (true, false, false), (false, true, false), (false, false, true)],
}

function solveSide(expression, makeSide, left, right):
	numWays <- 0;
	for operation_index, operation in enumerate(expression):
		for truthLookup in truthTable[operation]:
			if truthLookup[2] == makeSide:
				makeLHS <- truthLookup[0];
				makeRHS <- truthLookup[1];
				// memoize following
				numWaysLHS <- solveSide(expression, makeLHS, 0, operation_index);
				numWaysRHS <- solveSide(expression, makeRHS, operation_index + 1, right);
				numWays <- numWays + numWaysLHS * numWaysRHS;
	return numWays;
	
	solveSide(expression, true, 0, expression.length - 1)
```








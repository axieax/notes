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
	# base case (no operations in expression)
	if there are no operations in expression:
		return expression == makeSide
	# recursive case
	numWays <- 0;
	for each operation in expression:
		for (makeLHS, makeRHS, evaluatesTo) in truthTable[operation]:
			if evaluatesTo == makeSide:
				// memoize following
				numWaysLHS <- solveSide(expression, makeLHS, 0, word_before_operation);
				numWaysRHS <- solveSide(expression, makeRHS, word_after_operation, right);
				numWays += numWaysLHS * numWaysRHS;
	return numWays;

solveSide(expression, true, 0, expression.length - 1)
```





```pseudocode
// For each OPERATION in the truth table, each truthLookup tuple represents (LHS_truth, RHS_truth, LHS_truth OPERATION RHS_truth)
truthTable = {
	'AND': [(true, true, true), (true, false, false), (false, true, false), (false, false, false)],
	'OR': [(true, true, true), (true, false, true), (false, true, true), (false, false, false)],
	'NAND': [(true, true, false), (true, false, true), (false, true, true), (false, false, true)],
	'NOR': [(true, true, false), (true, false, false), (false, true, false), (false, false, true)],
}

function solveSide(expression, makeSide, left, right):
	# base case (no operations in expression)
	if (num_operations in expression) == 0:
		return expression == makeSide
	# recursive case
	numWays <- 0;
	for operation_index, operation in enumerate(expression):		
		for truthLookup in truthTable[operation]:
			if truthLookup[2] == makeSide:
				makeLHS <- truthLookup[0];
				makeRHS <- truthLookup[1];
				// memoize following
				numWaysLHS <- solveSide(expression, makeLHS, 0, operation_index);
				numWaysRHS <- solveSide(expression, makeRHS, operation_index + 1, right);
				if 
				numWays <- numWays + numWaysLHS * numWaysRHS;
	return numWays;
	
	solveSide(expression, true, 0, expression.length - 1)
```








# COMP3121 Assignment 3 Question 1

Andrew Xie, z5317337

**You are given a boolean expression consisting of a string of the symbols *true*, *false*, separated by operators AND, OR, NAND and NOR but without any parantheses. Count the number of ways one can put parantheses in the expression such that it will evaluate to *true*.**

Say we have an expression such as *false* AND *true* OR *true*. There are two ways to put parantheses in the expression, i.e. 

1. (*false*) AND (*true* OR *true*)
2. (*false* AND *true*) OR *true*.

In the first case, we are splitting around the AND operator, which requires both sides (LHS (*false*) and RHS (*true* OR *true*)) to be *true* in order for the whole expression to be *true*. Although there is one way of making the RHS (*true* OR *true*) *true*, there are no ways of making the LHS *true*, so there are no ways of making the first case evaluate to *true*. 

In the second case, we are splitting around the OR operator, which requires at least one side to be *true*. Looking up in a truth table, there are three ways of making an OR operator evaluate to *true*:

1. **LHS *true* OR RHS *true***
2. LHS *true* OR RHS *false*
3. **LHS *false* OR RHS *true*.**

In this case, the RHS is *true* so the number of ways to make case two *true* will be the number of ways to make the LHS *true* plus the number of ways to make the LHS *false* (bolded above). Observe that each of these act as subproblems themselves. The LHS (*false* AND *true*) can only be *false* since once again, since splitting around the only AND operator requires both sides to be *true* in order for the subexpression to be *true*. Hence, there is only one way of making case two *true*. 

To obtain the solution for the problem, we will need to sum up the number of ways in each case. Since the first case cannot be evaluated to *true*, there is only one way of putting parantheses in the expression such that it will evaluate to *true*, given by the second case. 

Generalising this algorithm to an expression with *n* symbols and *n - 1* operators between them, the number of ways to make the whole expression *true* is given by summing up the number of ways to make each operator *true* for each operator we split by (where everything to the left of the operator becomes a paranthesised LHS, and similarly for the RHS). This can be looked up in the truth table for the operators AND, OR, NAND and NONR. The number of ways to make each LHS and RHS *true* or *false* becomes another recursive subproblem. If this involves finding the number of ways to make a subproblem *false*, we will then have to sum up the number of ways to make each operator *false*, and so on until we reach the base case where there are no operators in the expression. At this stage, we will be evaluating the number of ways to make either symbols *true* or *false*, *true* or *false*. If the symbol is what we want the subexpression (base case) to be, then return 1 since there is only one way to make the symbol evaluate to the expected truth. Otherwise return 0. This recursive algorithm allows us to find the number of ways to put parantheses in the expression such that the whole expression will evaluate to *true*. 

==TIME COMPLEXITY n^3==

<u>Pseudocode:</u>

```pseudocode
// For each OPERATION in the truth table, each truthLookup tuple represents (LHS_truth, RHS_truth, LHS_truth OPERATION RHS_truth)
truthTable = {
	'AND': [(true, true, true), (true, false, false), (false, true, false), (false, false, false)],
	'OR': [(true, true, true), (true, false, true), (false, true, true), (false, false, false)],
	'NAND': [(true, true, false), (true, false, true), (false, true, true), (false, false, true)],
	'NOR': [(true, true, false), (true, false, false), (false, true, false), (false, false, true)],
}

function solveSide(expression, makeSide, left, right):
	# base case
	if there are no operations in expression:
		return expression == makeSide
	# recursive case
	numWays <- 0;
	for each operation in expression:
		for (makeLHS, makeRHS, evaluatesTo) in truthTable[operation]:
			if evaluatesTo == makeSide:
				# the following can be memoised to cache repeated computations
				numWaysLHS <- solveSide(expression, makeLHS, 0, word_before_operation);
				numWaysRHS <- solveSide(expression, makeRHS, word_after_operation, right);
				numWays += numWaysLHS * numWaysRHS;
	return numWays;

solveSide(expression, true, 0, expression.length - 1)
```



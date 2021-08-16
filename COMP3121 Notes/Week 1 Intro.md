# Algorithm

- Collection of precisely defined steps that are executable using certain specified mechanical methods (not involving any creativity, intuition or even intelligence)
- Sequential deterministic - given as sequence of steps (assuming only one step can be executed at a time), and the action of each step gives the same result whenever the step is executed for the same input
- Mathematical proof that an algorithm designed terminates and returns a solution to the problem at hand - needed when not obvious by inspecting the algorithm using common sense
- Proofs needed because:
  - Sometimes not clear whether algorithm will enter an infinite loop and fail to terminate
  - Sometimes not clear whether algorithm will not run in exponentially many steps in the size of the input - usually almost as bad as never terminating
  - Sometimes not clear whether algorithm produces a desired solution after it terminates

## Thieves loot splitting problem

Three Thieves:

```pseudocode
T1 makes a pile P1 which he believes is 1/3 of the whole loot;
T1 proceeds to ask T2 if T2 agrees that P1 ≤ 1/3;
If T2 says YES, then T1 asks T3 if T3 agrees that P1 ≤ 1/3;
	If T3 says YES, then T1 takes P1;
		T2 and T3 split the rest as in Problem 1.
	Else if T3 says NO, then T3 takes P1;
		T1 and T2 split the rest as in Problem 1.
Else if T2 says NO, then T2 reduces the size of P1 to P2 < P1 such that T2 thinks P2 = 1/3;
	T2 then proceeds to ask T3 if he agrees that P2 ≤ 1/3;
		If T3 says YES then T2 takes P2;
			T1 and T3 split the rest as in Problem 1.
		Else if T3 says NO then T3 takes P2;
			T1 and T2 split the rest as in Problem 1.
```

n thieves generalisation:

```pseudocode
T1 makes a pile P1 which he believes is 1/n of the whole loot;
T1 proceeds to ask the remaining thieves if they agree that P1 <= 1/n;
If everyone says YES, then T1 takes P1 and the remaining thieves split the rest similarly (recursively);
Whoever says NO reduces the size of P1 to P2 < P1 such that they think P2 = 1/n;
They proceed to ask the remaining thieves if they agree that P2 <= 1/n;
If everyone says YES, then T2 takes P2 and the remaining thieves split the rest similarly (recursively).
...
If the last thief says YES then Tn takes Pn and the rest is split amongst the remaining thieves similarly.
Else the last thief takes Pn and the remaining thieves after Tn and the rest is split amongst the remaining thieves.
```





## Merge sort

```pseudocode
Merge-Sort(A,p,r):
if p < r
	then q <- floor((p + r) / 2)
		MERGE-SORT(A,p,q) // sort first half recursively
		MERGE-SORT(A,q+1,r) // sort second half recursively
		MERGE(A,p,q,r) // merge sorted halves
```

- Proof by induction:

  - Depth of recursion: $log_2n$
  - On each step, merging immediate arrays takes $O(n)$ steps
  - Always terminates, with $O(log_2n)$ steps

  - Merging two sorted arrays always produces a sorted array, thus the output of Merge-Sort will be a sorted array



## Stable matching problem

- A set of $n$ pairs $p = (m, w)$ of a man $m$ and a woman $w$ so that the following situation never happens:
  - For two pairs $p = (m,w)$ and $p′ = (m′,w′)$:
    - Man $m$ prefers woman $w′$ to woman $w$, and
    - Woman $w′$ prefers man $m$ to man $m′$

### Gale - Shapley Algorithm

- Produces pairs in stages, with possible revisions.
- A man who has not been paired with a woman will be called free.
- Men will be proposing to women. Women will decide if they accept a proposal or not.
- Start with all men free.

```pseudocode
While there exists a free man who has not proposed to all women
	pick such a free man m and have him propose to the highest ranking woman w on his list to whom he has not proposed yet;
	If no one has proposed to w yet
		she always accepts and a pair p = (m, w) is formed;
	Else she is already in a pair p′ = (m′, w);
		If m is higher on her preference list than m′ the pair p′ = (m′, w) is deleted;
			m′ becomes a free man;
			a new pair p = (m, w) is formed;
		Else m is lower on her preference list than m′;
			the proposal is rejected and m remains free.
```

Proof:

- Claim 1: Algorithm terminates after $\leq n^2$ rounds of the while loop
  - In every round of the while loop, one man proposes to one woman
  - Every man can propose to a woman at most once
  - Thus, every man can make at most n proposals
  - There are n men, so in total they can make $\leq n^2$ proposals
  - Thus, the while loop can be executed no more than $n^2$ times
- Claim 2: Algorithm produces a matching, i.e., every man is eventually paired with a woman (and thus also every woman is paired to a man)
  - Assume that the while While loop has terminated, but m is still free.
  - This means that m has already proposed to every woman.
  - Thus, every woman is paired with a man, because a woman is not paired with anyone only if no one has made a proposal to her.
  - But this would mean that n women are paired with all of n men so m cannot be free. **Contradiction!**
- Claim 3: The matching produced by the algorithm is stable
  - Notes
    - A woman is paired with men of increasing ranks on her list
    - A man is paired with women of increasing ranks on his list
  - Proof by contradiction
    - Assume matching not stable, i.e. there are two pairs $p = (m, w)$ and $p' = (m', w')$ such that:
      - m prefers w' over w
      - w' prefers m over m'
    - Since m prefers w' over w, he must have proposed to w' before proposing to w
    - Since he is paired with w, woman w' must have either:
      - Rejected him because she was already with someone whom she prefers, or
      - Dropped him later after a proposal from someone whom she prefers
    - In both cases, she would now be with m' whom she prefers over m. **Contradiction!**




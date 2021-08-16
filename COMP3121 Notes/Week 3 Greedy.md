# Activity selection problem 1

Instance: a list of activities $a_i$, $(1 \leq i \leq n)$ with starting times $s_i$ and finishing times $f_i$. No two activities can take place simultaneously.

Task: Find a maximum size subset of compatible activities.

Solution: among the activities which do not conflict with the previously chosen activities, always choose the one with the earliest end time.

- Represent activities by ordered pairs of their starting and finishing times and sort them in an increasing order of their finishing time $O(n \log n)$
- Going through the sorted list, look for the first activity whose starting time is after the finishing time of the last taken activity
  - Every activity is handled only once $\to O(n)$
- Therefore algorithm has a time complexity of $O(n \log n)$

# Proving optimality of a greedy solution

Show that any optimal solution can be transformed into the greedy solution with equal number of activities:

- find the first place where the chosen activity violates the greedy choice
- show that replacing that activity with the greedy choice produces a non conflicting selection with the same number of activities
- continue in this manner until you "morph" your optimal solution into the greedy solution, thus proving the greedy solution is also optimal

# Activity selection problem 2

Activities all have the same duration - maximise total duration (would be achieved when num activities is maximised)

If not all the activities have the same duration - greedy strategy no longer works - use dynamic programming instead

# Minimising job lateness

Instance: A start time $T_0$ and a list of jobs $a_i, (1 \leq i \leq n)$ with duration times $t_i$ and deadlines $d_i$. Only one job can be performed at any time; all jobs have to be completed. If a job $a_i$ is completed at a finishing time $f_i \gt d_i$ then we say it has incurred lateness $l_i = f_i - d_i$.

Task: Schedule all the jobs so that the lateness of the job with the largest lateness is minimised.

Solution: Ignore job durations and schedule jobs in the increasing order of deadlines.

Optimality: consider any optimal solution. We say that jobs $a_i$ and $a_j$ form an inversion if job $a_i$ is scheduled before $a_j$, but $d_j \lt d_i$

- show there exists a scheduling without inversions which is also optimal
- use bubble sort to eliminate all inversions - keep swapping adjacent inversions
  - can't get a worse solution since duration will remain the same, but reduces the larger lateness

# Tape storage 1

Instance: a list of $n$ files $f_i$ is of lengths $l_i$ which have to be stored on a tape. Each file is equally likely to be needed. To retrieve the file, one must start from the beginning of the tape and scan it until the file is found and read.

Task: order the files on the tape so that the average (expected) retrieval time is minimised.

Solution: if the files are stored in order $l_1, l_2, ..., l_n$ then the expected time is proportional to $l_1 + (l_1 + l_2) + (l_1 + l_2 + l_3) + ... (l_1 + l_2 + l_3 + ... + l_n) = nl_1 + (n-1)l_2 + (n-2)l_3 + ... + 2l_{n-1} + l_n$. This is minimised if $l_1 \leq l_2 \leq l_3 \leq ... \leq l_n$. 

# Tape storage 2

Each file has a probability to be needed $p_i$. If the files are sorted in order $l_1, l_2, ..., l_n$, then the expected time is proportional to $l_1 p_1 + (l_1 + l_2)p_2 + (l_1 + l_2 + l_3)p_3 + ... + (l_1 + l_2 + l_3 + ... + l_n)p_n$.

We now show that this is minimised if the files are ordered in a decreasing order of values of the ratio $p_i / l_i$.

Observe swapping of two adjacent files - swap decreases the expected time just in case $p_k / l_k \lt p_{k+1} / l_{k+1}$, i.e. if there is an inversion: a file $f_{k+1}$ with a larger ratio $p_{k+1} / l_{k+1}$ has been put after a file $f_k$ with a smaller ratio $p_k / l_k$. 







Two methods:

Exchange argument

Greedy always ahead





# Binary Search Trees (BST)

---

### 1. Problem Context: The Runway Reservation Problem

Consider an Air Traffic Control (ATC) runway management system where flight landing/takeoff requests arrive at random times.

* **Primary Requirement:** Process the earliest request first (Priority Queue behavior).
* **Safety Constraint:** No two scheduled events can occur within a minimum separation window of **$\Delta = 3$ minutes** (to prevent collisions).

```text
              Existing Schedule:   [ 16:12 ]      [ 16:18 ]      [ 16:23 ]      [ 16:53 ]
                                                                                   ^
              New Request:                                                      [ 16:55 ]
                                                                                (INVALID! 16:55 - 16:53 = 2 min < 3 min)

```

#### Why Heaps Fail This Constraint

In a Min-Heap, finding the immediate **Predecessor** or **Successor** of a newly inserted request requires an $\mathcal{O}(N)$ linear scan because heaps do not maintain a global total order across branches.

---

### 2. Operational Comparison Across Data Structures

| Operation | Unsorted Array | Sorted Array | Min-Heap | Binary Search Tree (BST) |
| --- | --- | --- | --- | --- |
| **Find Min / Max** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | $\mathcal{O}(1)$ / $\mathcal{O}(N)$ | $\mathbf{\mathcal{O}(H)}$ |
| **Insert** | $\mathcal{O}(1)$ | $\mathcal{O}(N)$ | $\mathcal{O}(\log N)$ | $\mathbf{\mathcal{O}(H)}$ |
| **Delete** | $\mathcal{O}(N)$ | $\mathcal{O}(N)$ | $\mathcal{O}(\log N)$ | $\mathbf{\mathcal{O}(H)}$ |
| **Search / Find** | $\mathcal{O}(N)$ | $\mathcal{O}(\log N)$ | $\mathcal{O}(N)$ | $\mathbf{\mathcal{O}(H)}$ |
| **Predecessor / Successor** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | $\mathcal{O}(N)$ | $\mathbf{\mathcal{O}(H)}$ |

*Where $H$ is the height of the tree. In a balanced BST, $H = \mathcal{O}(\log N)$; in a degenerate BST, $H = \mathcal{O}(N)$.*

---

### 3. Binary Search Tree Invariant

A **Binary Search Tree** is a node-based binary tree data structure where each node contains a key/value and pointer fields (`left`, `right`, `parent`).

#### BST Property

For every node $u$ with key $K(u)$:

* All keys in the **left subtree** are strictly smaller: $\forall v \in \text{LeftSubtree}(u), \, K(v) < K(u)$.
* All keys in the **right subtree** are strictly larger: $\forall w \in \text{RightSubtree}(u), \, K(w) > K(u)$.

```text
                                [ 52 ]
                               /      \
                        [ 37 ]          [ 74 ]
                       /      \               \
                   [ 16 ]    [ 39 ]          [ 91 ]
                       \
                      [ 28 ]

```

---

### 4. Core BST Operations

#### A. In-Order Traversal (Sorted Output)

Recursively visit `LeftSubtree` $\to$ `CurrentNode` $\to$ `RightSubtree`.

* **Property:** An in-order traversal yields all elements in strictly **sorted ascending order** in $\mathcal{O}(N)$ time.

#### B. Search (`Find(v)`)

* Compare key $v$ with current node $t$.
* If $v < K(t)$, search left; if $v > K(t)$, search right.
* **Complexity:** $\mathcal{O}(H)$.

#### C. Min and Max

* **`FindMin(t)`:** Follow `left` pointers continuously until `t.left == NULL`.
* **`FindMax(t)`:** Follow `right` pointers continuously until `t.right == NULL`.

#### D. Successor & Predecessor

* **Successor (Next Largest Element):**
1. **If Right Subtree Exists:** Return `FindMin(t.right)`.
2. **If No Right Subtree:** Move up parent pointers until exiting a **left child link** (the first ancestor whose left child is also an ancestor of $t$).


* **Predecessor (Next Smallest Element):**
1. **If Left Subtree Exists:** Return `FindMax(t.left)`.
2. **If No Left Subtree:** Move up parent pointers until exiting a **right child link**.



```text
  Case 1: Right Subtree Exists               Case 2: No Right Subtree
            [ u ]                                      [ Ancestor ]  <-- SUCCESSOR!
                 \                                     /
                 [...]                               [...]
                 /                                   /
            [ Min Node ]  <-- SUCCESSOR!          [ u ]

```

#### E. Deletion (`Delete(v)`)

When deleting a node $t$ containing key $v$:

1. **Case 1: Node $t$ is a Leaf (No Children):**
* Simply set the parent's corresponding child pointer to `NULL`.


2. **Case 2: Node $t$ Has One Child:**
* Bypass $t$ by connecting $t$'s parent directly to $t$'s child.


3. **Case 3: Node $t$ Has Two Children:**
* Find $t$'s **In-Order Predecessor** $p = \text{FindMax}(t.\text{left})$ (or Successor).
* Overwrite $t$'s value with $p$'s value: $K(t) \gets K(p)$.
* Recursively delete $p$ from the left subtree (guaranteed to fall into Case 1 or Case 2 because $p$ has at most one child).



```text
  Delete(37) with Two Children:
  1. Locate Predecessor of 37 -> Max of Left Subtree = 28
  2. Overwrite 37 with 28.
  3. Delete original 28 node (which has at most one child).

```

---

### 5. Summary of BST Performance

All standard BST operations (`Search`, `Insert`, `Delete`, `Min`, `Max`, `Successor`, `Predecessor`) run in **$\mathcal{O}(H)$** time where $H$ is the height of the tree.

* **Best/Average Case (Balanced Tree):** $H = \mathbf{\mathcal{O}(\log N)}$
* **Worst Case (Degenerate / Skewed Tree):** $H = \mathbf{\mathcal{O}(N)}$


---





# Balanced Search Trees (AVL Trees)

---

### 1. Motivation & Height vs. Size Balance

In a standard Binary Search Tree (BST), the time complexity of all core operations (`Find`, `Insert`, `Delete`, `Min`, `Max`, `Predecessor`, `Successor`) is bounded by the height of the tree $H$. In the worst-case (skewed tree), $H = \mathcal{O}(N)$, destroying the efficiency guarantees.

* **Size-Based Balance (Too Rigid):** Enforcing that the left and right subtrees have the exact same number of nodes produces complete binary trees, which are overly restrictive and difficult to maintain during dynamic updates.
* **Height-Based Balance (AVL Trees):** Enforcing that the heights of the left and right subtrees differ by at most 1 provides enough flexibility while ensuring $H = \mathbf{\mathcal{O}(\log N)}$.

#### Height Definition Convention

To distinguish an empty tree from a single-node tree cleanly:

* **Empty Tree ($\text{NIL}$):** $\text{Height} = 0$
* **Single Node (Root only):** $\text{Height} = 1$
* **General Node $u$:** $\text{Height}(u) = 1 + \max(\text{Height}(u.\text{left}), \text{Height}(u.\text{right}))$

---

### 2. AVL Tree Property & Balance Factor (Slope)

An **AVL Tree** (named after Adelson-Velsky and Landis) is a height-balanced BST.

#### Balance Factor / Slope ($\text{Slope}(u)$)

$$\text{Slope}(u) = \text{Height}(u.\text{left}) - \text{Height}(u.\text{right})$$

* **Invariant:** For every node $u$ in an AVL tree, the slope must satisfy:

$$\text{Slope}(u) \in \{-1, 0, +1\}$$


* **Imbalance Trigger:** Following an `Insert` or `Delete` operation, a node's slope can temporarily become **$+2$** (left-heavy) or **$-2$** (right-heavy).

```text
                        Balanced (Slope = +1):            Unbalanced (Slope = +2):
                                 [ 5 ]                             [ 5 ]
                                /     \                           /     \
                             [ 3 ]   [ 7 ]                     [ 3 ]   [ 7 ]
                            /                                 /
                         [ 2 ]                             [ 3.5 ]
                                                          /
                                                       [ 2 ]

```

---

### 3. Rebalancing Operations (Tree Rotations)

Rebalancing is performed **bottom-up** starting from the newly modified node back up toward the root. When encountering a node $X$ with $\text{Slope}(X) = +2$ or $-2$, balance is restored using tree rotations.

#### A. Single Rotations (Case 1: Same Sign Slopes)

* **Left-Left (LL) Heavy ($\text{Slope}(X) = +2$, $\text{Slope}(X.\text{left}) \ge 0$):**
* Solution: Perform a **Single Right Rotation at $X$**.



```text
       [ X ] (+2)                                [ Y ] (0)
      /     \           Right Rotation          /     \
   [ Y ] (+1) TR       ===============>      [ T_L ]  [ X ] (0)
   /   \                                             /   \
 [T_L] [T_LR]                                     [T_LR] [T_R]

```

* **Right-Right (RR) Heavy ($\text{Slope}(X) = -2$, $\text{Slope}(X.\text{right}) \le 0$):**
* Solution: Perform a **Single Left Rotation at $X$**.



---

#### B. Double Rotations (Case 2: Opposite Sign Slopes)

* **Left-Right (LR) Heavy ($\text{Slope}(X) = +2$, $\text{Slope}(X.\text{left}) = -1$):**
* Solution: Perform a **Left Rotation at $Y$ (left child)**, followed by a **Right Rotation at $X$**.



```text
         [ X ] (+2)                             [ X ] (+2)                           [ Z ] (0)
        /     \         Left Rotate at Y       /     \         Right Rotate at X    /     \
     [ Y ] (-1) TR     ================>    [ Z ] (+1) TR     =================> [ Y ]   [ X ]
     /   \                                  /   \                               /  \   /   \
   T_L   [ Z ]                            [ Y ] tlrr                          T_L tlrl tlrr T_R
        /     \                          /    \
      tlrl   tlrr                      T_L    tlrl

```

* **Right-Left (RL) Heavy ($\text{Slope}(X) = -2$, $\text{Slope}(X.\text{right}) = +1$):**
* Solution: Perform a **Right Rotation at $Y$ (right child)**, followed by a **Left Rotation at $X$**.



---

### 4. Implementation Details: Height Field Maintenance

* **Avoid $\mathcal{O}(N)$ Recomputation:** Computing tree height recursively during slope checks would take $\mathcal{O}(N)$ time, ruining performance.
* **$\mathcal{O}(1)$ Height Tracking:** Each tree node explicitly stores its own `height` field.
* During rotations, the heights of only the affected roots ($X, Y, Z$) are recalculated in $\mathcal{O}(1)$ constant time using the stored heights of their children.

---

### 5. Summary of AVL Performance

| Operation | Time Complexity | Space Complexity |
| --- | --- | --- |
| **Search / Find** | $\mathcal{O}(\log N)$ | $\mathcal{O}(1)$ |
| **Insert (with Rebalancing)** | $\mathcal{O}(\log N)$ | $\mathcal{O}(\log N)$ stack recursion |
| **Delete (with Rebalancing)** | $\mathcal{O}(\log N)$ | $\mathcal{O}(\log N)$ stack recursion |
| **Min / Max** | $\mathcal{O}(\log N)$ | $\mathcal{O}(1)$ |
| **Predecessor / Successor** | $\mathcal{O}(\log N)$ | $\mathcal{O}(1)$ |



---




# Interval Scheduling (Greedy Paradigm)

---

### 1. Problem Formulation

The **Interval Scheduling Problem** asks us to select the maximum number of mutually compatible intervals (or tasks/requests) from a set $B = \{1, 2, \dots, n\}$.

* Each job $i$ is defined by a start time $s_i$ and a finish time $f_i$ ($s_i < f_i$).
* Two jobs $i$ and $j$ are **compatible** (non-overlapping) if $f_i \le s_j$ or $f_j \le s_i$.
* **Objective:** Find a subset $A \subseteq B$ of pairwise compatible jobs such that $\vert{}A\vert{}$ is **maximized**.

```text
               Job 1: [---]                     Selected: Job 1, Job 3, Job 5
               Job 2:   [-----]                 
               Job 3:       [---]               (Maximizes total non-overlapping jobs)
               Job 4:         [---]
               Job 5:             [--]

```

---

### 2. Flawed Greedy Strategies (Counterexamples)

When designing greedy algorithms, local choice rules can easily lead to suboptimal global solutions.

| Strategy Rule | Proposed Choice | Counterexample Scenario | Resulting Suboptimality |
| --- | --- | --- | --- |
| **1. Earliest Start Time** | Pick job $i$ with minimum $s_i$. | One long job covers the entire timeline while many short jobs occur inside it. | Picks **1** job instead of **$N$** short jobs. |
| **2. Shortest Duration** | Pick job $i$ with minimum $(f_i - s_i)$. | A short central job overlaps two longer non-overlapping jobs on either side. | Picks **1** short job instead of **2** side jobs. |
| **3. Fewest Conflicts** | Pick job $i$ overlapping the minimum number of remaining jobs. | A job with 2 conflicts blocks 4 non-overlapping jobs split across levels. | Picks **3** jobs instead of **4** optimal jobs. |

---

### 3. Optimal Strategy: Earliest Finish Time (EFT)

The optimal greedy strategy always selects the compatible job that **finishes earliest** ($f_i$ is minimized). This leaves the maximum possible remaining time for subsequent jobs.

#### Algorithm Pseudocode

```text
Greedy_Interval_Scheduling(B):
    1. Sort all n jobs by ascending finish times (f_1 <= f_2 <= ... <= f_n)  -- O(n log n)
    2. A = {}                                                                 -- Accepted set
    3. last_finish_time = -infinity

    4. for i = 1 to n:
          if s_i >= last_finish_time:                                         -- Non-overlapping
              A = A U {i}
              last_finish_time = f_i

    5. return A

```

---

### 4. Proof of Correctness ("Greedy Stays Ahead")

Let $A = \{i_1, i_2, \dots, i_k\}$ be the set of jobs selected by the Greedy algorithm, sorted by finish times.
Let $O = \{j_1, j_2, \dots, j_m\}$ be an optimal set of jobs, sorted by finish times.

#### Induction Claim

For every $r \le k$, the finish time of the $r$-th greedy job is no later than the finish time of the $r$-th optimal job:


$$f(i_r) \le f(j_r) \quad \text{}$$

* **Base Case ($r = 1$):**
The greedy algorithm picks $i_1$ with the global minimum finish time across all jobs. Thus, $f(i_1) \le f(j_1)$.
* **Inductive Step:**
Assume $f(i_{r-1}) \le f(j_{r-1})$.
Since $O$ is valid, $s(j_r) \ge f(j_{r-1}) \ge f(i_{r-1})$.
This implies $j_r$ is a valid, non-overlapping candidate at step $r$ for the greedy algorithm.
Because greedy selects the candidate with the earliest finish time:

$$f(i_r) \le f(j_r) \quad \text{}$$



#### Final Contradiction Proof

Suppose $O$ has strictly more jobs than $A$, i.e., $m > k$.
By our claim, $f(i_k) \le f(j_k)$. Since $m > k$, there exists job $j_{k+1} \in O$ such that:


$$s(j_{k+1}) \ge f(j_k) \ge f(i_k) \quad \text{}$$


This means $j_{k+1}$ is compatible with $i_k$ and was available after step $k$. Thus, greedy could not have stopped at $k$ jobs. Contradiction! Hence, $m = k$, proving $A$ is optimal.

---

### 5. Time & Space Complexity

* **Sorting Jobs by Finish Time:** $\mathcal{O}(n \log n)$
* **Single Scan for Selection:** $\mathcal{O}(n)$
* **Overall Time Complexity:** **$\mathcal{O}(n \log n)$**
* **Space Complexity:** $\mathcal{O}(n)$ (to store inputs/outputs).

---






# Scheduling with Deadlines: Minimizing Lateness

---

### 1. Problem Definition & Formulation

In the **Minimizing Lateness** scheduling problem, we are given a single resource and $n$ requests/jobs.

* **Job Parameters:** Each job $i$ has a processing duration $t_i > 0$ and a deadline $d_i$.
* **Schedule Execution:** All $n$ jobs must be scheduled consecutively on the resource. If job $i$ starts at time $s_i$, it finishes at time $f_i = s_i + t_i$.
* **Lateness ($L_i$):** A job $i$ is late if it finishes after its deadline $d_i$:

$$L_i = \max(0, f_i - d_i)$$


* **Objective:** Design a schedule that **minimizes the maximum lateness** across all jobs:

$$\text{Minimize } L = \max_{i} L_i$$



```text
Job i:   [--- Processing Time t_i ---]
         ^                           ^                   ^
      Start (s_i)                 Finish (f_i)      Deadline (d_i)

Lateness L_i = max(0, f_i - d_i)

```

---

### 2. Flawed Greedy Strategies

| Strategy | Selection Rule | Counterexample Scenario | Suboptimal Outcome |
| --- | --- | --- | --- |
| **Shortest Processing Time** | Pick job $i$ with minimum duration $t_i$ first. | Job 1 ($t_1=1, d_1=100$), Job 2 ($t_2=10, d_2=10$). | Scheduling Job 1 first causes Job 2 to finish at $11 \implies \text{Lateness} = 1$. Optimal gives $\text{Lateness} = 0$. |
| **Smallest Slack Time** | Pick job $i$ with minimum slack $(d_i - t_i)$ first. | Job 1 ($t_1=1, d_1=2 \implies \text{slack}=1$), Job 2 ($t_2=10, d_2=10 \implies \text{slack}=0$). | Scheduling Job 2 first causes Job 1 to finish at $11 \implies \text{Lateness} = 9$. Optimal gives $\text{Lateness} = 1$. |

---

### 3. Optimal Strategy: Earliest Deadline First (EDF)

The optimal greedy rule schedules jobs in non-decreasing order of their deadlines:

$$d_1 \le d_2 \le \dots \le d_n$$

#### Algorithmic Steps

1. Sort all $n$ jobs by deadline $d_i$ in non-decreasing order.
2. Initialize start time $s = 0$.
3. For each job $i = 1 \dots n$:
* Assign $s_i = s$.
* Assign $f_i = s + t_i$.
* Update $s \gets f_i$.



---

### 4. Correctness Proof via Exchange Argument

#### Lemma 1: No Idle Time

There exists an optimal schedule with no idle gaps between jobs. Compressing any gap by shifting subsequent jobs earlier can only reduce or maintain finish times and lateness.

#### Lemma 2: Inversion Definition & Existence

An **inversion** in a schedule $S$ is a pair of jobs $i$ and $j$ such that $i$ is scheduled before $j$, but $d_j < d_i$ (a job with a later deadline is executed before a job with an earlier deadline).

If a schedule has an inversion, it must contain at least one **adjacent inversion** where $i$ immediately precedes $j$ and $d_j < d_i$.

#### Exchange Step

Let $S$ be an optimal schedule with an adjacent inversion $(i, j)$ ending at time $f_j$:

```text
Before Swap (S):     [ ... ] [   Job i   ] [ Job j ] [ ... ]
                                           ^
                                        Finish f_j

After Swap (S'):    [ ... ] [ Job j ] [   Job i   ] [ ... ]
                                           ^
                                        Finish f_j

```

1. **Unchanged Jobs:** Jobs before $i$ and after $j$ have unchanged start and finish times.
2. **Exchanged Jobs ($i, j$):**
* Job $j$ finishes earlier in $S'$ than in $S \implies L'_j \le L_j$.
* Job $i$ now finishes at time $f_j$ in $S'$.
* Since $d_j < d_i$, the new lateness of job $i$ is:

$$L'_i = f_j - d_i < f_j - d_j = L_j$$


* Thus, $L'_i < L_j$, meaning the new max lateness of the pair does not exceed the original max lateness of job $j$ in $S$.



#### Conclusion

Swapping adjacent inverted jobs removes one inversion without increasing the maximum lateness of the schedule. By repeatedly applying this exchange argument (at most $\binom{n}{2}$ times), we transform any optimal schedule $O$ into the greedy EDF schedule $A$ without increasing maximum lateness. Hence, $A$ is optimal.

---

### 5. Time & Space Complexity

* **Sorting Jobs by Deadline:** $\mathcal{O}(n \log n)$
* **Scheduling Execution:** $\mathcal{O}(n)$
* **Overall Time Complexity:** **$\mathcal{O}(n \log n)$**
* **Space Complexity:** $\mathcal{O}(n)$



---





# Huffman Coding (Greedy Data Compression)

---

### 1. Motivation: Fixed-Length vs. Variable-Length Codes

When transmitting data over digital channels, characters must be converted into binary strings ($0$ and $1$).

* **Fixed-Length Encoding:** Assigns the exact same number of bits to every character.
* *Example:* For a 26-letter English alphabet, $\lceil \log_2 26 \rceil = 5$ bits per character are required ($2^5 = 32 \ge 26$).
* *Inflexibility:* Frequent characters (like 'E' or 'A') consume the same space as rare characters (like 'Z' or 'Q').


* **Variable-Length Encoding:** Assigns **fewer bits to more frequent characters** and **more bits to rarer characters**, reducing the total expected file size.

---

### 2. The Prefix-Free Property

To decode a variable-length bitstream unambiguously without needing extra delimiter characters (like pauses or spaces), the code must satisfy the **Prefix Property**:

$$\text{No valid code word is a prefix of any other valid code word.} \quad \text{}$$

#### Unambiguous Decoding Tree

Prefix-free codes map directly onto **Full Binary Trees** where:

1. **Internal Nodes:** Represent decision branches ($0 = \text{Left}$, $1 = \text{Right}$).
2. **Leaf Nodes:** Store the actual alphabet characters. Because characters reside solely at leaves, no character's path can extend into another's path.

```text
                             Root (Sum = 1.00)
                            /                 \
                     0     /                   \  1
                          /                     \
                  Node (0.43)                 Node (0.57)
                 /           \               /           \
          0     /             \ 1     0     /             \ 1
               /               \           /               \
            [ 'A' ]         [ 'B' ]     [ 'C' ]          Node (0.23)
            (0.20)          (0.23)      (0.34)          /           \
                                                 0     /             \ 1
                                                      /               \
                                                   [ 'D' ]         [ 'E' ]
                                                   (0.18)          (0.05)

```

---

### 3. Mathematical Optimization Objective

Let $C$ be the alphabet of $k$ characters. For each character $x \in C$, let:

* $f(x)$ = Frequency / probability of character $x$ occurring.
* $d_T(x)$ = Depth of leaf $x$ in tree $T$ (length of $x$'s code word in bits).

The **Average Code Length / Expected Cost $B(T)$** to minimize is:

$$B(T) = \sum_{x \in C} f(x) \cdot d_T(x) \quad \text{}$$

---

### 4. Huffman's Algorithm Mechanics

Huffman's algorithm uses a **bottom-up greedy strategy**: repeatedly combine the **two lowest-frequency nodes** into a single merged meta-node until only one root node remains.

#### Algorithm Pseudocode

```text
Huffman(C):
    1. Initialize a Min-Priority Queue Q with all k characters in C sorted by frequency f(x). -- O(k) or O(k log k)
    
    2. for i = 1 to k - 1:
          x = ExtractMin(Q)                             -- Lowest frequency character 1
          y = ExtractMin(Q)                             -- Lowest frequency character 2
          
          create new internal node z
          z.left = x
          z.right = y
          f(z) = f(x) + f(y)                            -- Cumulative weight
          
          Insert(Q, z)                                  -- Push meta-node back
          
    3. return ExtractMin(Q)                             -- Final root of the optimal tree

```

---

### 5. Proof Structure: Structural Invariants & Inductive Proof

#### Key Structural Lemma (Optimal Substructure)

Let $x$ and $y$ be the two characters in $C$ with the absolute lowest frequencies ($f(x) \le f(y) \le f(z), \forall z \in C \setminus \{x,y\}$). There exists an optimal prefix code tree $T$ in which $x$ and $y$ are **sibling leaves at maximum depth**.

#### Cost Shift Equation (Inductive Step)

When merging $x$ and $y$ into parent node $z$ with weight $f(z) = f(x) + f(y)$, the cost $B(T)$ of the original tree relates to the cost $B(T')$ of the reduced tree over $k-1$ characters by a constant additive shift:

$$B(T) = B(T') + f(x) + f(y) \quad \text{}$$

Since $f(x) + f(y)$ is fixed by our greedy choice of the two minimal elements, minimizing $B(T')$ inductively guarantees the minimization of $B(T)$.

---

### 6. Time and Space Complexity

* **Priority Queue Implementation:** Min-Heap.
* **Building Initial Min-Heap:** $\mathcal{O}(k)$ time.
* **Main Loop Iterations:** Performs $k - 1$ merges. Each step does two `ExtractMin` and one `Insert` operation on a heap of max size $k \implies \mathcal{O}(\log k)$.
* **Overall Time Complexity:** **$\mathcal{O}(k \log k)$** for an alphabet of $k$ distinct characters.
* **Space Complexity:** $\mathcal{O}(k)$ to store the tree nodes and priority queue.
# Introduction to Dynamic Programming

---

### 1. Inductive Definitions & Optimal Substructure

Dynamic Programming (DP) builds upon **inductive definitions** (expressing a problem's solution in terms of smaller instances of the same problem).

#### Optimal Substructure Property

A problem exhibits **optimal substructure** if an optimal solution to the problem contains optimal solutions to its underlying subproblems.

* **Factorial Example (Inductive):**

$$\text{Fact}(n) = n \cdot \text{Fact}(n - 1) \quad \text{with base case } \text{Fact}(0) = 1 \quad \text{}$$


* **Insertion Sort (Structural Substructure):**
To sort $A[1 \dots n]$, recursively sort sub-array $A[2 \dots n]$, then insert $A[1]$ into its correct sorted position.

---

### 2. Breakdown of the Greedy Approach vs. DP

Consider the **Weighted Interval Scheduling Problem**:

* Given $n$ jobs, where each job $i$ has start time $s_i$, finish time $f_i$, and an associated **weight / value $v_i$**.
* **Objective:** Find a subset of mutually compatible (non-overlapping) jobs that **maximizes the total weight** $\sum v_i$.

```text
               Job 1:  [ v1 = 1 ]                     (Earliest finish time)
               Job 2:      [   v2 = 3   ]             (Highest weight, overlaps 1 & 3)
               Job 3:          [ v3 = 1 ]

               Greedy (EFT) Picks: Job 1 + Job 3  ==> Total Weight = 2 (SUBOPTIMAL!)
               Optimal Picks:      Job 2          ==> Total Weight = 3

```

#### Why Greedy Fails

In unweighted interval scheduling, picking the earliest finish time (EFT) reduces the state space from $2^n$ subsets down to $\mathcal{O}(n)$ choices. However, in the **weighted version**, local greedy choices (like earliest finish time or highest weight) can discard the globally optimal subset.

---

### 3. Inductive Case Analysis (Choice Decision)

For any given job $b_1$, an optimal solution either **includes $b_1$** or **excludes $b_1$**:

```text
                                OPT(1...n)
                              /            \
                   Include b1              Exclude b1
                  /                          \
   v1 + OPT(Compatible Jobs after b1)      OPT(b2...bn)

```

1. **Option 1 (Exclude $b_1$):** The subproblem reduces to finding the optimal schedule using remaining jobs $\{b_2, b_3, \dots, b_n\}$.
2. **Option 2 (Include $b_1$):** Add $v_1$ to the total weight and eliminate all jobs overlapping with $b_1$. The subproblem reduces to finding the optimal schedule among the remaining non-conflicting jobs.

$$\text{OPT}(1) = \max\Big( v_1 + \text{OPT}(\text{First Compatible Job After } b_1), \, \text{OPT}(2) \Big) \quad \text{}$$

---

### 4. Overlapping Subproblems & Solution Approaches

When recursively computing choices using naive recursion, the algorithm generates the **same subproblems multiple times along different decision branches**.

```text
                              OPT(1..n)
                             /         \
                      Include b1     Exclude b1
                          /               \
                    OPT(3..n)          OPT(2..n)
                                       /        \
                                Include b2    Exclude b2
                                    /              \
                              OPT(3..n)          OPT(3..n)
                              ^ REPEATED WORK! ^

```

* **Naive Recursion Complexity:** Exponential $\mathcal{O}(2^n)$ due to re-evaluating identical subproblems.
* **Dynamic Programming / Memoization Goal:** Evaluate each unique subproblem **exactly once** and store the result in a table (lookup array), turning an exponential time search into polynomial time.

---

### 5. Two Core DP Implementation Approaches

| Feature | Top-Down DP (Memoization) | Bottom-Up DP (Tabulation) |
| --- | --- | --- |
| **Execution Flow** | Recursion with a caching lookup table. | Iterative loop filling an array from base cases up. |
| **Subproblem Computation** | Computes only subproblems reachable on demand. | Systematically solves all subproblems in order of dependency. |
| **Overhead** | Function call stack overhead. | Fast execution with simple array iterations. |

---



# Memoization and Dynamic Programming

---

### 1. The Redundant Computation Problem

When evaluating an inductive definition using naive recursion, identical subproblems are re-evaluated multiple times across different recursive branches.

#### Fibonacci Case Study

The Fibonacci sequence is inductively defined as:

* $F(0) = 0, \quad F(1) = 1$
* $F(n) = F(n-1) + F(n-2) \quad \text{for } n \ge 2$

```text
                                 F(5)
                               /      \
                           F(4)        F(3)  <-- REPETITION!
                          /    \      /    \
                       F(3)   F(2)  F(2)   F(1)
                      /   \
                   F(2)   F(1)

```

* **Subproblem Explode:** Calculating $F(5)$ requires computing $F(3)$ twice, $F(2)$ three times, and $F(1)$ five times.
* **Complexity:** Naive recursion executes in **exponential time $\mathcal{O}(2^n)$** (specifically $\mathcal{O}(\phi^n)$ where $\phi \approx 1.618$), despite there being only **$n + 1$ unique subproblems**.

---

### 2. Strategy 1: Memoization (Top-Down Caching)

**Memoization** preserves the top-down structure of naive recursion but introduces an explicit **lookup table (memo table)** to cache and reuse previously computed results.

#### Execution Flow

1. Before performing recursive calls for input $n$, check if $n$ already exists as a key in the lookup table.
2. **Hit:** Return the cached value immediately in $\mathcal{O}(1)$ time.
3. **Miss:** Compute the result recursively, store the calculated value into the table under key $n$, and return it.

```text
Memoized_Fib(n, MemoTable):
    if MemoTable[n] exists:
        return MemoTable[n]                     -- Cache Hit
        
    if n <= 1:
        f = n
    else:
        f = Memoized_Fib(n - 1) + Memoized_Fib(n - 2)
        
    MemoTable[n] = f                            -- Store Cache
    return f

```

* **Complexity:** Reduces computation time from $\mathcal{O}(2^n)$ to **$\mathcal{O}(n)$ time**, using $\mathcal{O}(n)$ space for the lookup table and call stack.

---

### 3. Strategy 2: Dynamic Programming (Bottom-Up Tabulation)

**Dynamic Programming (Tabulation)** eliminates recursion entirely. It identifies subproblem dependencies, treats them as a **Directed Acyclic Graph (DAG)**, and fills a table iteratively in topological order (from base cases upward).

#### Subproblem DAG & Dependency Chain

For Fibonacci, subproblem dependencies flow strictly from smaller to larger indices:

$$F(0), F(1) \longrightarrow F(2) \longrightarrow F(3) \longrightarrow F(4) \longrightarrow F(5)$$

#### Tabular Implementation

```text
Iterative_Fib(n):
    Table[0] = 0
    Table[1] = 1
    
    for i = 2 to n:
        Table[i] = Table[i - 1] + Table[i - 2]   -- Bottom-up evaluation
        
    return Table[n]

```

* **Time Complexity:** **$\mathcal{O}(n)$**
* **Space Complexity:** $\mathcal{O}(n)$ (can be optimized to $\mathcal{O}(1)$ space by maintaining only the last two computed terms).

---

### 4. Technical Comparison: Memoization vs. Tabulation

| Feature | Memoization (Top-Down) | Tabulation (Bottom-Up / DP) |
| --- | --- | --- |
| **Control Flow** | Recursive with cache checks | Iterative looping |
| **Subproblem Evaluation** | Computes only subproblems on demand along the search path | Computes all table entries in strict dependency order |
| **Function Call Overhead** | Incurs system stack overhead for recursive calls | No stack overhead; runs fast in practice |
| **Data Structure** | Hash maps, dictionaries, or sparse arrays | Contiguous 1D/2D arrays |


---



# Dynamic Programming: Grid Paths Counting & Obstacles

---

### 1. Problem Formulation: Grid Paths

Given an $m \times n$ rectangular grid, count the total number of distinct paths from the **bottom-left corner $(0,0)$** to the **top-right corner $(m,n)$**.

#### Structural Movement Constraints:

* Allowed movements from any intersection $(i, j)$:
1. **Up ($\uparrow$):** Move to $(i, j+1)$
2. **Right ($\rightarrow$):** Move to $(i+1, j)$


* Total moves required to reach $(m,n)$ from $(0,0)$:

$$\text{Total Moves} = m + n \quad (m \text{ Right moves and } n \text{ Up moves})$$



```text
                  (0,2) ------- (1,2) ------- (2,2) [Destination]
                    |             |             |
                    |             |             |
                  (0,1) ------- (1,1) ------- (2,1)
                    |             |             |
                    |             |             |
  [Source] (0,0) ------- (1,0) ------- (2,0)

```

---

### 2. Analytical Combinatorial Solution

Without obstacles, the number of distinct grid paths is given by the binomial coefficient:

$$\text{Paths}(m, n) = \binom{m + n}{m} = \binom{m + n}{n} = \frac{(m + n)!}{m! \cdot n!}$$

#### Example ($2 \times 2$ Grid):

$$\text{Paths}(2, 2) = \binom{2 + 2}{2} = \binom{4}{2} = \frac{4 \times 3}{2 \times 1} = 6$$

---

### 3. Dynamic Programming Formulation

#### A. Recurrence Relation

To arrive at intersection $(i, j)$, the final step must originate from either:

1. Below $(i, j-1)$ via an **Up** move.
2. The left $(i-1, j)$ via a **Right** move.

$$\text{Paths}(i, j) = \text{Paths}(i - 1, j) + \text{Paths}(i, j - 1)$$

#### B. Base Boundary Conditions

Along the bottom row ($j=0$) or left column ($i=0$), there is only $1$ way to navigate (straight line of Right or Up moves):

* $\text{Paths}(i, 0) = 1 \quad \forall i \in \{0, \dots, m\}$
* $\text{Paths}(0, j) = 1 \quad \forall j \in \{0, \dots, n\}$

---

### 4. Handling Grid Obstacles (Blocked Intersections)

If certain grid intersections $(x, y)$ contain obstacles (hole/blocked zone), no path can pass through $(x, y)$.

#### Modified Recurrence Rule:

$$\text{Paths}(i, j) = \begin{cases}  0 & \text{if } (i, j) \text{ is BLOCKED} \\ \text{Paths}(i-1, j) + \text{Paths}(i, j-1) & \text{otherwise} \end{cases}$$

```text
  Tabular Evaluation Matrix (m=2, n=2) with Obstacle at (1,1):

       j = 2 |    1    |   1 + 0 = 1  |  1 + 1 = 2
       j = 1 |    1    |  0 (BLOCKED) |  0 + 1 = 1
       j = 0 |    1    |      1       |      1
             +---------+--------------+------------
               i = 0       i = 1          i = 2

  Total Valid Paths to (2,2) with obstacle at (1,1) = 2

```

---

### 5. Memoization vs. Tabulation Comparison in Grid Paths

| Parameter | Memoization (Top-Down Recursion) | Tabulation (Bottom-Up Dynamic Programming) |
| --- | --- | --- |
| **Space Traversal** | Evaluates **only reachable subproblems** on demand. If large blocks are obstacle-blocked, whole regions are ignored. | Systematically fills **all $m \times n$ table cells** row-by-row / column-by-column regardless of obstacles. |
| **Stack Overhead** | Call stack space $\mathcal{O}(m + n)$ (path depth). | No recursion stack overhead. |
| **When to Prefer?** | Prefer when large regions of the grid are unreachable due to dense obstacles. | Prefer when the grid has sparse/no obstacles for simpler iteration loops. |

#### Time & Space Complexity (Tabulated):

* **Time Complexity:** $\mathcal{O}(m \cdot n)$
* **Space Complexity:** $\mathcal{O}(m \cdot n)$ (Can be optimized to $\mathcal{O}(\min(m, n))$ space using 1D row/column arrays).


---




# Common Subwords & Subsequences (LCS)

---

### 1. Longest Common Subword (LCSW)

#### Problem Definition

Given two strings $U = a_0 a_1 \dots a_m$ and $V = b_0 b_1 \dots b_n$, find the maximum length of a contiguous block (subword) appearing in both strings.

```text
U = "secret"      (m = 5)
V = "secretary"   (n = 8)
--> Longest Common Subword: "secret" (Length = 6)

```

#### Recurrence Relation

Let $\text{LCSW}(i, j)$ be the length of the longest common subword starting at positions $a_i$ in $U$ and $b_j$ in $V$.

$$\text{LCSW}(i, j) = \begin{cases}  0 & \text{if } i > m \text{ or } j > n \text{ (Boundary Base Case)} \\ 1 + \text{LCSW}(i+1, j+1) & \text{if } a_i = b_j \\ 0 & \text{if } a_i \neq b_j \text{ (Contiguity broken)} \end{cases} \quad \text{}$$

#### Tabulation Matrix Mechanics

* **Subproblem Dependencies:** Entry $(i, j)$ depends directly on the **bottom-right neighbor** $(i+1, j+1)$.
* **Result Extraction:** The overall answer is $\max_{i, j} \text{LCSW}(i, j)$ across the entire table.

```text
                b_0 b_1 b_2 ... b_n  n+1
            +---------------------------+
       a_0  |   \                       |
       a_1  |    \  (1 + diag)          |
            |     v                     |
       a_m  |                           |
       m+1  | 0   0   0   0 ...  0   0  |
            +---------------------------+

```

---

### 2. Longest Common Subsequence (LCS)

#### Problem Definition

Given two strings $U = a_0 a_1 \dots a_m$ and $V = b_0 b_1 \dots b_n$, find the maximum length of a sequence that appears in both strings in the same relative order, allowing non-contiguous characters (dropping letters).

```text
U = "bisect"
V = "secret"
--> Longest Common Subword:     "sec"  (Length = 3)
--> Longest Common Subsequence: "sect" (Length = 4)

```

#### Real-World Applications

* **Bioinformatics:** DNA alignment ($\text{A, T, G, C}$ sequence matching across genomes).
* **Unix `diff` Command:** Line-by-line file comparison to compute minimal code changes.

---

### 3. LCS Recurrence Relation

Let $\text{LCS}(i, j)$ be the length of the longest common subsequence starting from suffix $U[i \dots m]$ and $V[j \dots n]$.

$$\text{LCS}(i, j) = \begin{cases}  0 & \text{if } i > m \text{ or } j > n \\ 1 + \text{LCS}(i+1, j+1) & \text{if } a_i = b_j \text{ (Greedy Match Principle)} \\ \max\Big(\text{LCS}(i+1, j), \, \text{LCS}(i, j+1)\Big) & \text{if } a_i \neq b_j \text{ (Drop character choice)} \end{cases} \quad \text{}$$

#### Why the Greedy Choice Works for $a_i = b_j$

If $a_i = b_j$, there is never a need to skip this match. Matching $a_i$ and $b_j$ immediately preserves maximum freedom for remaining suffix character matches without causing crossing pointer links.

---

### 4. LCS Dynamic Programming Grid

#### Subproblem Dependencies

Entry $(i, j)$ depends on three neighboring cells:

1. **Diagonal:** $(i+1, j+1)$ [Used on match]
2. **Below:** $(i+1, j)$ [Used on mismatch: skip $a_i$]
3. **Right:** $(i, j+1)$ [Used on mismatch: skip $b_j$]

```text
                  (i, j) ------> (i, j+1)
                    |         /    |
                    |       /      |
                    v     v        v
                  (i+1, j) ----> (i+1, j+1)

```

#### Evaluation Order & Answer Location

* Fill table bottom-up and right-to-left starting from base boundary rows $i = m+1$ and $j = n+1$ set to $0$.
* **Final Result:** Stored at **$\text{LCS}(0, 0)$**.

---

### 5. Algorithmic Complexity

| Metric               | Brute Force Search            | Dynamic Programming                                                               |
| -------------------- | ----------------------------- | --------------------------------------------------------------------------------- |
| **LCSW Time**        | $\mathcal{O}(m \cdot n^2)$    | **$\mathcal{O}(m \cdot n)$**                                                      |
| **LCS Time**         | $\mathcal{O}(2^{\max(m, n)})$ | **$\mathcal{O}(m \cdot n)$**                                                      |
| **Space Complexity** | $\mathcal{O}(1)$              | **$\mathcal{O}(m \cdot n)$** (Can be reduced to $\mathcal{O}(\min(m, n))$ space). |


---






# Edit Distance (Levenshtein Distance)

---

### 1. Problem Formulation

The **Edit Distance** (or **Levenshtein Distance**) measures the minimum number of character-level edit operations required to transform one string $U = a_0 a_1 \dots a_m$ into another string $V = b_0 b_1 \dots b_n$.

#### Allowed Operations

1. **Insert:** Add a character into $U$ (Cost = 1).
2. **Delete:** Remove a character from $U$ (Cost = 1).
3. **Substitute:** Replace a character in $U$ with a different character from $V$ (Cost = 1 if different, Cost = 0 if identical).

```text
Transforming "bisect" to "secret":
  1. Delete 'b'   --> "isect"
  2. Delete 'i'   --> "sect"
  3. Insert 'r'   --> "srect"
  4. Insert 'e'   --> "secret"
Total Edit Operations = 4

```

#### Applications

* **Spell Checkers / Autocorrect:** Suggest nearest dictionary words based on minimal edit distance.
* **Bioinformatics:** DNA alignment to measure evolutionary distance between species.
* **Document Comparison:** Diff tools tracking insertions, deletions, and line modifications.

---

### 2. Recurrence Relation

Let $E(i, j)$ be the edit distance between suffixes $U[i \dots m]$ and $V[j \dots n]$.

$$\text{EditDistance}(i, j) = \begin{cases}  n - j + 1 & \text{if } i = m + 1 \text{ (U is empty, insert remaining } V \text{ chars)} \\ m - i + 1 & \text{if } j = n + 1 \text{ (V is empty, delete remaining } U \text{ chars)} \\ E(i+1, j+1) & \text{if } a_i = b_j \text{ (No edit required)} \\ 1 + \min \begin{cases}  E(i+1, j+1) & \text{[Substitute } a_i \text{ with } b_j\text{]} \\ E(i+1, j) & \text{[Delete } a_i\text{]} \\ E(i, j+1) & \text{[Insert } b_j\text{]} \end{cases} & \text{if } a_i \neq b_j \end{cases} \quad \text{}$$

---

### 3. Dynamic Programming Grid & Tracing Solutions

#### Subproblem Dependencies

Just like LCS, each cell $(i, j)$ depends on three neighbors:

* **Diagonal $(i+1, j+1)$:** Match / Substitution
* **Below $(i+1, j)$:** Deletion
* **Right $(i, j+1)$:** Insertion

```text
                  (i, j) ------ Insert -----> (i, j+1)
                    |                       /    |
                    |                     /      |
                 Delete                 Sub      |
                    |                 /          v
                    v               v         (i+1, j+1)
                 (i+1, j) ------------------>

```

#### Final Answer Location & Backtracking

* **Result Position:** **$E(0, 0)$** contains the total minimal edit distance.
* **Reconstructing Edits (Backtracking):** Follow the local minimum choices from $E(0,0)$ back to $(m+1, n+1)$:
* Moving **Diagonal ($\searrow$)** $\implies$ Match or Replace.
* Moving **Down ($\downarrow$)** $\implies$ Delete character $a_i$.
* Moving **Right ($\rightarrow$)** $\implies$ Insert character $b_j$.



---

### 4. Space Complexity Optimization

Instead of storing the full $(m+1) \times (n+1)$ 2D matrix, notice that computing column $j$ only requires values from column $j+1$.

```text
  Full Table: O(m * n) Space               Optimized: O(min(m, n)) Space
     +---+---+---+---+                        +---+---+
     |   |   |   |   |                        | C | N |   (Only 2 columns
     +---+---+---+---+   ===============>     +---+---+    or 2 rows kept
     |   |   |   |   |                        | C | N |    in memory)
     +---+---+---+---+                        +---+---+

```

#### Complexity Analysis

* **Time Complexity:** **$\mathcal{O}(m \cdot n)$** (filling $m \times n$ table cells).
* **Naive Space Complexity:** $\mathcal{O}(m \cdot n)$.
* **Optimized Space Complexity:** **$\mathcal{O}(\min(m, n))$** space by retaining only 2 columns or 2 rows at a time.


---





# Matrix Chain Multiplication (Dynamic Programming)

---

### 1. Problem Formulation

The **Matrix Chain Multiplication** problem aims to determine the optimal parenthesization (associative grouping) for multiplying a sequence of $n$ matrices $M_1, M_2, \dots, M_n$ to minimize the total scalar multiplications required.

#### Matrix Multiplication Basics

* To multiply an $m \times n$ matrix $A$ by an $n \times p$ matrix $B$, the inner dimensions must match ($n$).
* The resulting matrix $AB$ has dimensions $m \times p$.
* **Cost:** Computing $A \times B$ requires **$m \cdot n \cdot p$** scalar multiplications.

```text
Matrix A: (10 x 100)      Matrix B: (100 x 5)      Matrix C: (5 x 50)

Option 1: (A * B) * C
  - Cost(A * B) = 10 * 100 * 5 = 5,000      --> Matrix (10 x 5)
  - Cost((A * B) * C) = 10 * 5 * 50 = 2,500
  - Total Scalar Multiplications = 7,500

Option 2: A * (B * C)
  - Cost(B * C) = 100 * 5 * 50 = 25,000     --> Matrix (100 x 50)
  - Cost(A * (B * C)) = 10 * 100 * 50 = 50,000
  - Total Scalar Multiplications = 75,000 (10x WORSE!)

```

---

### 2. Recurrence Relation & Subproblem Structure

Let matrix $M_i$ have dimensions $r_i \times c_i$.
Since adjacent matrices are compatible, $c_i = r_{i+1}$.
Let $\text{Cost}(i, j)$ be the minimum number of scalar multiplications needed to compute the sub-chain product $M_i \times M_{i+1} \times \dots \times M_j$.

#### Splitting Choice ($k$)

At the outermost level of sub-chain $M_i \dots M_j$, the final matrix multiplication splits the chain at some position $k$ where $i \le k < j$:


$$(M_i \dots M_k) \times (M_{k+1} \dots M_j) \quad \text{}$$

#### Recurrence Formula

$$\text{Cost}(i, j) = \begin{cases}  0 & \text{if } i = j \text{ (Base case: single matrix, no multiplication)} \\ \min_{i \le k < j} \Big( \text{Cost}(i, k) + \text{Cost}(k+1, j) + (r_i \cdot c_k \cdot c_j) \Big) & \text{if } i < j \end{cases} \quad \text{}$$

```text
                               Cost(i, j)
                             /     |      \
                       k = i    k = i+1 ... k = j-1
                         /         |          \
           [Cost(i,k) + Cost(k+1,j) + (r_i * c_k * c_j)]

```

---

### 3. Dynamic Programming Table Mechanics

Unlike 2D string alignment tables filled row-by-row or column-by-column, matrix chain multiplication evaluates subproblems by **chain length $L = j - i + 1$** (along diagonals).

```text
                     j --->  1      2      3   ...   n
               i +------------------------------------+
               1 |           0    L=2    L=3  ...    n|
               2 |           x      0    L=2  ...  L=3|
               3 |           x      x      0  ...  L=2|
               . |           .      .      .   \    . |
               n |           x      x      x  ...    0|
                 +------------------------------------+
                                                        ^
                                              Answer at Cost(1, n)

```

#### Evaluation Steps

1. **Base Case:** Set main diagonal $\text{Cost}(i, i) = 0$ for all $i \in \{1, \dots, n\}$.
2. **Diagonal Traversal:** Fill table entries for increasing sub-chain lengths $L = 2, 3, \dots, n$.
3. **Target Entry:** The final minimum scalar cost is found at **$\text{Cost}(1, n)$**.

---

### 4. Algorithmic Complexity Breakdown

#### Time Complexity: $\mathcal{O}(n^3)$

* **Subproblem Table Size:** $\mathcal{O}(n^2)$ unique entries in the upper-triangular matrix.
* **Computation Per Entry:** To fill $\text{Cost}(i, j)$, the inner loop tests all split positions $k$ between $i$ and $j-1$, taking $\mathcal{O}(j - i) = \mathcal{O}(n)$ steps.
* **Total Time Complexity:** $\mathcal{O}(n^2) \times \mathcal{O}(n) = \mathbf{\mathcal{O}(n^3)}$.

> **Key Takeaway:** Matrix Chain Multiplication demonstrates that dynamic programming execution time is **not always equal to the table size**; individual table entries may require non-constant time to compute.

#### Space Complexity: $\mathcal{O}(n^2)$

* Requires an $n \times n$ table to store optimal sub-chain cost values and split decisions ($k$ indices).


---

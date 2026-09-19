## Lecture Notes: Introduction and Motivation

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Introduction and Motivation

---

### 1. Core Concepts & Definitions

* **Design vs. Analysis:**
* **Design:** Formulating a step-by-step procedure (algorithm) to solve a given computational problem.
* **Analysis:** Estimating the efficiency of the algorithm based on computational resource constraints.


* **Time vs. Space Complexity:**
* **Time:** How long the algorithm takes to execute. This is the primary focus because CPU sequential clock speeds are physically constrained and difficult to scale dynamically.
* **Space:** Memory required during execution. Space is a more flexible constraint because hardware memory (RAM) can easily be expanded.


* **Measuring Time:**
* *Absolute wall-clock time* (seconds/milliseconds) is non-robust because it varies across hardware, operating systems, compilers, and programming languages.
* Time is measured abstractly by counting **atomic operations** (steps) as a function of input size $n$, denoted as $T(n)$.


* **Basic Operations (Atomic Steps):** High-level language primitives like variable assignment (`x = y + 1`) or comparison (`a < b`) are treated as single steps.
* **Worst-Case Estimate:** $T(n)$ evaluates the maximum execution time over *all possible inputs* of size $n$, providing a guaranteed upper bound on performance.

---

### 2. Theoretical Baseline (Standard Machine Capabilities)

To evaluate practical running times, a typical modern CPU is assumed to execute approximately **$10^8$ basic operations per second**.

| Operation Count | Time Required | Usability Status |
| --- | --- | --- |
| **$10^8$ ops** | 1 second | Instantaneous / High Usability |
| **$10^9$ ops** | ~10 seconds | Acceptable Practical Limit |
| **$10^{10}$ ops** | ~100 seconds | Borderline / Slow |
| **$10^{18}$ ops** | ~300 years | Completely Impractical |

---

### 3. Practical Impact: $\mathcal{O}(n^2)$ vs. $\mathcal{O}(n \log n)$ Case Studies

#### Case 1: Telecom Subscriber Database Sorting ($n = 10^9$)

* **Brute-Force / Naive Sorting ($\mathcal{O}(n^2)$):**

$$\text{Operations} = (10^9)^2 = 10^{18}$$


$$\text{Time} = \frac{10^{18}}{10^8 \text{ ops/sec}} = 10^{10} \text{ seconds} \approx \mathbf{300 \text{ years}}$$


* **Optimized Sorting ($\mathcal{O}(n \log_2 n)$):**
$$\log_2(10^9) \approx 30$$

$$\text{Operations} = 30 \times 10^9 = 3 \times 10^{10}$$

$$\text{Time} = \frac{3 \times 10^{10}}{10^8 \text{ ops/sec}} = 300 \text{ seconds} = \mathbf{5 \text{ minutes}}$$



#### Case 2: Closest-Pair Search in Game Engine ($n = 5 \times 10^5$ objects)

* **Pairwise Comparison ($\mathcal{O}(n^2)$):**

$$\text{Operations} = (5 \times 10^5)^2 = 25 \times 10^{10}$$


$$\text{Time} = \frac{25 \times 10^{10}}{10^8 \text{ ops/sec}} = 2500 \text{ seconds} \approx \mathbf{41.6 \text{ minutes}}$$


* **Divide & Conquer Approach ($\mathcal{O}(n \log_2 n)$):**

$$\text{Time} \approx \mathbf{0.01 \text{ to } 0.1 \text{ seconds}} \quad (\text{Real-time rendering ready})$$



---

### 4. Asymptotic Growth & Practical Feasibility

Constant factors are ignored in asymptotic analysis because higher-order growth rates dominate as input size $n$ scales.

#### Input Size Feasibility Limits (at $10^8$ ops/sec CPU speed)

| Complexity Class | $T(n)$ Notation | Feasible Input Size Boundary ($n$) | Practical Example / Domain |
| --- | --- | --- | --- |
| **Logarithmic** | $\mathcal{O}(\log n)$ | $n > 10^{10}$ | Binary Search |
| **Linear** | $\mathcal{O}(n)$ | $n \approx 10^8 - 10^9$ | Single Pass Search, Counting |
| **Linearithmic** | $\mathcal{O}(n \log n)$ | $n \approx 10^8$ | Merge Sort, Quick Sort |
| **Quadratic** | $\mathcal{O}(n^2)$ | $n \approx 10^4$ | Bubble Sort, Pairwise Check |
| **Cubic** | $\mathcal{O}(n^3)$ | $n \approx 10^3$ | Naive Matrix Multiplication |
| **Exponential** | $\mathcal{O}(2^n)$ | $n \le 20$ | Brute Force / Subset Search |
| **Factorial** | $\mathcal{O}(n!)$ | $n \le 10$ | Permutation Search (TSP) |

---

### 5. Key Takeaways

1. **Hardware cannot save bad algorithms:** Hardware clock speeds face physical limitations; scalable performance must come from algorithmic efficiency rather than faster CPUs.
2. **The Polynomial Boundary:** Algorithms running in polynomial time ($\mathcal{O}(n^k)$ for small $k$) scale well to large datasets, whereas exponential and factorial algorithms fail even on small inputs ($n > 30$).
3. **Worst-Case Focus:** Analyzing worst-case scenarios guarantees that an algorithm performs within predictable performance boundaries regardless of input distribution.


---

## Lecture Notes: Input Size, Worst Case, Average Case

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Input Size, Worst Case, Average Case

---

### 1. Defining Input Size ($n$)

Input size $n$ measures the amount of memory or space required to write down and represent the input data for a problem.

* **Arrays / Lists:** $n$ corresponds to the total number of elements.
* **Knapsack / Item Selection:** $n$ corresponds to the total number of items available for selection.
* **Graphs:** Represented using two distinct parameters:
* $V$ (or $N$): Number of vertices (nodes/cities).
* $E$ (or $M$): Number of edges (connections/flights).



#### Special Case: Operations on Numbers

When an input itself is a numerical value $N$ (e.g., primality testing), the input size is **not** the magnitude $N$, but the number of bits or digits required to represent $N$:


$$\text{Input Size } n = \log_2(N) \quad (\text{or } \log_{10}(N))$$

* An algorithm running in time proportional to $N$ runs in time exponential relative to its input size: $T(n) = \mathcal{O}(2^n)$.
* An algorithm is efficient only if its running time is a polynomial function of the bit-length $n$ (i.e., polynomial in $\log N$).

---

### 2. Basic Operations vs. Constant Multipliers

Analysis abstracts away small constant factors (e.g., treating $3n$ vs $n$ as equivalent asymptotic orders) because the explicit choice of basic operation varies based on execution context:

* A high-level swap operation `swap(x, y)` might require 3 assignment operations at the instruction level:
```
temp = x;
x = y;
y = temp;

```


* Ignoring small constant multiplicative factors ensures that algorithmic complexity bounds remain robust and independent of low-level machine implementation details.

---

### 3. Execution Complexity Cases

The running time $T(n)$ depends not only on the input size $n$, but also on the specific arrangement/structure of the input data.

```
                   Input Set of Size 'n'
              /             |            \
             /              |             \
   Best Case            Average Case          Worst Case
   (Minimum steps)     (Expected value)     (Maximum steps)

```

#### A. Linear Search Walkthrough

Given an un-ordered array of size $n$, search for a target value $v$:

```
i = 0
while i < n:
    if A[i] == v:
        return True
    i = i + 1
return False

```

1. **Best-Case Execution:**
* **Scenario:** Target $v$ is at the first index (`A[0]`).
* **Complexity:** $\mathcal{O}(1)$ steps (instantaneous match).
* **Utility:** Provides little practical insight because it relies on unrealistically favorable conditions.


2. **Worst-Case Execution:**
* **Scenario:** Target $v$ is at the last index (`A[n-1]`) or not present in the array at all.
* **Complexity:** $\mathcal{O}(n)$ steps (scans all $n$ items).
* **Utility:** Guarantees a upper bound on execution time. The algorithm will never perform worse than this threshold under any circumstances.


3. **Average-Case Execution:**
* **Scenario:** Expected running time over all valid inputs of size $n$.
* **Complexity:** $\frac{n + 1}{2} \approx \mathcal{O}(n)$ steps (assuming uniform probability distribution).



---

### 4. Why Focus on Worst-Case Analysis?

Worst-case analysis is the primary focus in algorithm analysis for two main reasons:

1. **Guaranteed Upper Bound:** It provides a strict performance guarantee. An algorithm tested under worst-case analysis will never unexpectedly stall or crash due to scale in production environments.
2. **Mathematical Feasibility:** Calculating the average case requires defining a formal probability distribution over all possible inputs. In real-world applications, input distributions are often unknown, non-uniform, or difficult to model accurately (e.g., real-world airline routes or search queries).

---

### 5. Summary Matrix of Cases

| Case | Definition | Mathematical Form | Practical Relevance |
| --- | --- | --- | --- |
| **Best Case** | Minimum running time across all inputs of size $n$ | $\min_{I \in S_n} T(I)$ | Low (overly optimistic) |
| **Worst Case** | Maximum running time across all inputs of size $n$ | $\max_{I \in S_n} T(I)$ | **High** (provides hard guarantees) |
| **Average Case** | Expected running time given an input distribution | $\sum_{I \in S_n} P(I) \cdot T(I)$ | Medium (hard to model mathematically) |


---

## Lecture Notes: Quantifying Efficiency — O( ), Omega( ), Theta( )

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Quantifying Efficiency: O( ), Omega( ), Theta( )

---

### 1. Formal Asymptotic Definitions

Asymptotic notation measures how the running time function $f(n)$ scales as the input size $n \to \infty$, ignoring hardware constants and low-order terms.

* **Big-O Notation ($\mathcal{O}$) — Asymptotic Upper Bound:**
* **Definition:** $f(n) = \mathcal{O}(g(n))$ if there exist positive constants $c > 0$ and $n_0 \ge 1$ such that:

$$f(n) \le c \cdot g(n) \quad \forall n \ge n_0$$


* **Meaning:** $f(n)$ grows **no faster than** $g(n)$. Provides a guaranteed worst-case performance ceiling.


* **Big-Omega Notation ($\Omega$) — Asymptotic Lower Bound:**
* **Definition:** $f(n) = \Omega(g(n))$ if there exist positive constants $c > 0$ and $n_0 \ge 1$ such that:

$$f(n) \ge c \cdot g(n) \quad \forall n \ge n_0$$


* **Meaning:** $f(n)$ grows **at least as fast as** $g(n)$. Defines the minimum execution floor.


* **Big-Theta Notation ($\Theta$) — Tight Bound:**
* **Definition:** $f(n) = \Theta(g(n))$ if and only if $f(n) = \mathcal{O}(g(n))$ and $f(n) = \Omega(g(n))$.
* **Mathematical Statement:** There exist positive constants $c_1, c_2 > 0$ and $n_0 \ge 1$ such that:

$$c_1 \cdot g(n) \le f(n) \le c_2 \cdot g(n) \quad \forall n \ge n_0$$


* **Meaning:** $f(n)$ grows at **the exact same rate as** $g(n)$.



---

### 2. Comprehensive Lecture Examples & Proof Steps

#### Example A: Proving an Upper Bound ($100n + 5 = \mathcal{O}(n^2)$)

* **Goal:** Find constants $c$ and $n_0$ such that $100n + 5 \le c \cdot n^2$.
* **Step-by-step logic:**
1. For any $n \ge 1$, we know $5 \le 5n$.
2. Therefore, $100n + 5 \le 100n + 5n = 105n$.
3. Since $n \le n^2$ for $n \ge 1$, $105n \le 105n^2$.
4. Picking **$c = 105$** and **$n_0 = 1$** satisfies $100n + 5 \le 105n^2$ for all $n \ge 1$.


* **Alternative Constant Choice:** For $n \ge 100$, $100n \le n^2$ and $5 \le n^2$, so $100n + 5 \le 2n^2$ (giving $c = 2, n_0 = 100$). The exact constants do not matter as long as one valid pair exists.

#### Example B: Disproving an Upper Bound ($n^3 \neq \mathcal{O}(n^2)$)

* **Goal:** Show that no fixed constant $c$ can bound $n^3$ by $c \cdot n^2$.
* **Step-by-step logic:**
1. Assume by contradiction that $n^3 = \mathcal{O}(n^2)$.
2. This implies $n^3 \le c \cdot n^2$ for all $n \ge n_0$.
3. Divide both sides by $n^2$, yielding $n \le c$ for all $n \ge n_0$.
4. **Contradiction:** $n$ grows infinitely large, while $c$ is a fixed, finite constant. Thus, $n^3$ can never be $\mathcal{O}(n^2)$.



#### Example C: Proving a Tight Bound ($\frac{n(n-1)}{2} = \Theta(n^2)$)

* **Function:** $f(n) = \frac{n^2 - n}{2}$ (occurs in nested loops comparing pairs).
* **Upper Bound ($\mathcal{O}$):**

$$\frac{n^2 - n}{2} \le \frac{n^2}{2} = \frac{1}{2} n^2 \quad \forall n \ge 1 \implies c_2 = \frac{1}{2}, n_0 = 1$$


* **Lower Bound ($\Omega$):**
For $n \ge 2$, we know that $(n - 1) \ge \frac{n}{2}$. Substituting this in:

$$\frac{n(n-1)}{2} \ge \frac{n \cdot (n/2)}{2} = \frac{1}{4} n^2 \quad \forall n \ge 2 \implies c_1 = \frac{1}{4}, n_0 = 2$$


* **Conclusion:** Since $\frac{1}{4} n^2 \le f(n) \le \frac{1}{2} n^2$ holds for all $n \ge 2$, $f(n) = \Theta(n^2)$.

---

### 3. Key Algebraic Properties

* **Sum Rule (Sequential Blocks):**
* If $f_1(n) = \mathcal{O}(g_1(n))$ and $f_2(n) = \mathcal{O}(g_2(n))$, then:

$$f_1(n) + f_2(n) = \mathcal{O}(\max(g_1(n), g_2(n)))$$


* *Meaning:* The slowest running component dominates the overall complexity.


* **Product Rule (Nested Structures):**
* If $f_1(n) = \mathcal{O}(g_1(n))$ and $f_2(n) = \mathcal{O}(g_2(n))$, then:

$$f_1(n) \cdot f_2(n) = \mathcal{O}(g_1(n) \cdot g_2(n))$$




* **Polynomial Dominance Rule:**
* For any polynomial $P(n) = a_k n^k + a_{k-1} n^{k-1} + \dots + a_0$ with $a_k > 0$:

$$P(n) = \Theta(n^k)$$


* Drop all lower-degree terms and constant coefficients.



---

### 4. Application Contexts: Algorithms vs. Problems

| Concept | Applies To | Notation | Explanation & Real Example |
| --- | --- | --- | --- |
| **Upper Bound** | **Algorithms** | $\mathcal{O}$ | Measures performance of a specific code approach.<br>

<br>• *Bubble Sort:* $\mathcal{O}(n^2)$<br>

<br>• *Merge Sort:* $\mathcal{O}(n \log n)$ |
| **Lower Bound** | **Problems** | $\Omega$ | Measures inherent difficulty of a problem regardless of algorithm.<br>

<br>• *Comparison Sorting Problem:* $\Omega(n \log n)$ (proven mathematical lower bound). |
| **Optimal Algorithm** | **Algorithm + Problem** | $\Theta$ | Achieved when an algorithm's upper bound matches the problem's lower bound.<br>

<br>• *Merge Sort* is $\Theta(n \log n)$ optimal for sorting. |

---

### 5. Asymptotic Hierarchy Reference

$$\mathcal{O}(1) < \mathcal{O}(\log n) < \mathcal{O}(n) < \mathcal{O}(n \log n) < \mathcal{O}(n^2) < \mathcal{O}(n^3) < \mathcal{O}(2^n) < \mathcal{O}(n!)$$


---


## Lecture Notes: Examples: Analysis of Iterative and Recursive Algorithms

**Course:** Design and Analysis of Algorithms

  

**Source:** NPTEL — Examples: Analysis of Iterative and Recursive Algorithms

  

### 1. Iterative Algorithm Analysis

#### Rule of the Bottleneck (Sum Rule)

When an algorithm consists of multiple sequential phases (e.g., initialization, loop, return), the phase with the highest growth rate determines the overall asymptotic time complexity:

  

$$\mathcal{O}(f_1(n) + f_2(n)) = \mathcal{O}(\max(f_1(n), f_2(n)))$$

#### Example 1: Finding Maximum Element (Linear Search)

- **Goal:** Find the maximum value in an array `A` of size $n$.
    
      
    
- **Algorithm Pseudocode:**
    
      
    
    Plaintext
    
    ```
    maxval = A[0]
    for i = 1 to n - 1:
        if A[i] > maxval:
            maxval = A[i]
    return maxval
    ```
    
- **Step-by-Step Analysis:**
    
      
    1. Initialization (`maxval = A[0]`) and return statement take $\mathcal{O}(1)$ time.
        
          
        
    2. The loop runs exactly $n - 1$ times.
        
          
        
    3. Inside the loop, 1 comparison is performed per iteration, and an assignment occurs conditionally.
        
          
        
    4. Letting $c$ be the maximum constant work per iteration:
        
          
        
        $$T(n) = c \cdot (n - 1) = c \cdot n - c$$
        
- **Complexity:** $\mathcal{O}(n)$
    
      
    

#### Example 2: Checking Array Element Uniqueness (Nested Loops)

- **Goal:** Determine if all elements in array `A` of size $n$ are distinct (no duplicates).
    
      
    
- **Algorithm Pseudocode:**
    
      
    
    Plaintext
    
    ```
    for i = 0 to n - 1:
        for j = i + 1 to n - 1:
            if A[i] == A[j]:
                return False
    return True
    ```
    
- **Step-by-step Analysis:**
    
      
    1. To prevent redundant comparisons, index $j$ scans only elements to the right of index $i$ ($j \in [i+1, n-1]$).
        
          
        
    2. Total iterations of inner loop across all values of $i$:
        
          
        - $i = 0 \implies (n - 1)$ comparisons
            
              
            
        - $i = 1 \implies (n - 2)$ comparisons
            
              
            
        - $i = n - 2 \implies 1$ comparison
            
              
            
        - $i = n - 1 \implies 0$ comparisons
            
              
            
    3. Total operation count is the summation of the first $(n-1)$ integers:
        
          
        
        $$T(n) = \sum_{k=1}^{n-1} k = \frac{(n-1)n}{2} = \frac{n^2}{2} - \frac{n}{2}$$
        
- **Complexity:** $\mathcal{O}(n^2)$
    
      
    

#### Example 3: Matrix Multiplication (Triple Nested Loops)

- **Goal:** Multiply two $n \times n$ matrices $A$ and $B$ to compute matrix $C = A \times B$.
    
      
    
- **Algorithm Pseudocode:**
    
      
    
    Plaintext
    
    ```
    for i = 0 to n - 1:
        for j = 0 to n - 1:
            C[i][j] = 0
            for k = 0 to n - 1:
                C[i][j] = C[i][j] + A[i][k] * B[k][j]
    ```
    
- **Step-by-step Analysis:**
    
      
    1. Outer loop ($i$): Executes $n$ times (iterates over rows of $A$).
        
          
        
    2. Middle loop ($j$): Executes $n$ times (iterates over columns of $B$).
        
          
        
    3. Inner loop ($k$): Executes $n$ times (computes dot product of row $i$ and column $j$).
        
          
        
    4. Total execution count:
        
          
        
        $$T(n) = n \times n \times n = n^3$$
        
- **Complexity:** $\mathcal{O}(n^3)$
    
      
    

#### Example 4: Counting Bits in Binary Representation (Logarithmic Loop)

- **Goal:** Determine the number of bits needed to write a positive integer $n$ in binary.
    
      
    
- **Algorithm Pseudocode:**
    
      
    
    Plaintext
    
    ```
    count = 1
    while n > 1:
        count = count + 1
        n = n // 2
    return count
    ```
    
- **Step-by-step Analysis:**
    
      
    1. In each iteration, $n$ is divided by 2 (integer division).
        
          
        
    2. The loop terminates when $n$ is reduced to $1$.
        
          
        
    3. The number of steps $k$ required to reduce $n$ to $1$ by dividing by $2$ satisfies:
        
          
        
        $$\frac{n}{2^k} = 1 \implies 2^k = n \implies k = \log_2(n)$$
        
- **Complexity:** $\mathcal{O}(\log_2 n)$
    
      
    

### 2. Recursive Algorithm Analysis & Recurrence Relations

#### Case Study: Towers of Hanoi

- **Problem Constraints:**
    
      
    - Move $n$ disks from source peg $A$ to target peg $B$ using auxiliary peg $C$.
        
          
        
    - Rule: Never place a larger disk on top of a smaller disk.
        
          
        
- **Recursive Strategy:**
    
      
    1. Move top $n - 1$ disks from peg $A$ to auxiliary peg $C$ (using peg $B$).
        
          
        
    2. Move the largest remaining disk ($n$-th disk) directly from peg $A$ to target peg $B$.
        
          
        
    3. Move $n - 1$ disks from auxiliary peg $C$ to target peg $B$ (using peg $A$).
        
          
        

Plaintext

```
       Step 1: Move n-1 disks              Step 2: Move 1 disk                Step 3: Move n-1 disks
       Peg A -> Peg C (using B)            Peg A -> Peg B                     Peg C -> Peg B (using A)

          |        |        |                 |        |        |                 |        |        |
         [ ]       |        |                 |        |        |                 |        |       [ ]
        [   ]      |        |                 |        |       [ ]                |        |      [   ]
       =======  =======  =======           =======  =======  =======           =======  =======  =======
        Peg A    Peg B    Peg C             Peg A    Peg B    Peg C             Peg A    Peg B    Peg C
```

#### Recurrence Formulation

Let $M(n)$ be the total number of moves required to solve Towers of Hanoi for $n$ disks:

  

- **Base Case:** $M(1) = 1$
    
      
    
- **Recurrence Relation:**
    
      
    
    $$M(n) = M(n-1) + 1 + M(n-1) = 2 \cdot M(n-1) + 1$$
    

#### Solving Recurrence via Repeated Substitution Method

Expand $M(n)$ by substituting the expression for $M(n-1)$, $M(n-2)$, etc.:

  

1. **Step 1:** $M(n) = 2 \cdot M(n-1) + 1$
    
      
    
2. **Step 2:** Substitute $M(n-1) = 2 \cdot M(n-2) + 1$:
    
      
    
    $$M(n) = 2 \cdot \left(2 \cdot M(n-2) + 1\right) + 1 = 2^2 \cdot M(n-2) + 2 + 1$$
    
3. **Step 3:** Substitute $M(n-2) = 2 \cdot M(n-3) + 1$:
    
      
    
    $$M(n) = 2^2 \cdot \left(2 \cdot M(n-3) + 1\right) + 2 + 1 = 2^3 \cdot M(n-3) + 2^2 + 2^1 + 2^0$$
    
4. **General Form after $k$ Substitutions:**
    
      
    
    $$M(n) = 2^k \cdot M(n-k) + \sum_{i=0}^{k-1} 2^i = 2^k \cdot M(n-k) + (2^k - 1)$$
    
5. **Set $k = n - 1$ to reach the base case $M(1)$:**
    
      
    
    $$M(n) = 2^{n-1} \cdot M(1) + (2^{n-1} - 1)$$
    
6. **Substitute base value $M(1) = 1$:**
    
      
    
    $$M(n) = 2^{n-1} \cdot 1 + 2^{n-1} - 1 = 2 \cdot 2^{n-1} - 1 = 2^n - 1$$
    

- **Complexity:** $\mathcal{O}(2^n)$ (Exponential Time)
    

### 3. Summary Comparison Table

|**Algorithm / Problem**|**Analysis Technique**|**Closed-Form Expression**|**Time Complexity**|
|---|---|---|---|
|**Linear Search (Max Element)**|Loop counting|$n - 1$ iterations|$\mathcal{O}(n)$|
|**Array Uniqueness**|Summing series $\sum_{1}^{n-1} k$|$\frac{n(n-1)}{2}$ iterations|$\mathcal{O}(n^2)$|
|**Matrix Multiplication**|Triple nested loops|$n^3$ operations|$\mathcal{O}(n^3)$|
|**Bit Counting**|Repeated division by 2|$\log_2 n$ divisions|$\mathcal{O}(\log_2 n)$|
|**Towers of Hanoi**|Substitution on recurrence $2M(n-1)+1$|$2^n - 1$ moves|$\mathcal{O}(2^n)$|
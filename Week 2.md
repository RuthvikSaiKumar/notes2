## Lecture Notes: Arrays and Lists

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Arrays and Lists

---

### 1. Memory Organization & Data Access Mechanics

To store a sequence of values $A = [A_0, A_1, \dots, A_{n-1}]$, two primary low-level memory layout strategies are used: contiguous memory allocation (Arrays) and node-pointer links (Linked Lists).

```text
ARRAY (Contiguous Memory Layout):
+---------+---------+---------+---------+---------+
|   A[0]  |   A[1]  |   A[2]  |   ...   | A[n-1]  |
+---------+---------+---------+---------+---------+
Base Addr   +1 Size   +2 Size              +(n-1) Size

LINKED LIST (Pointer-Chained Nodes):
+---+---+    +---+---+    +---+---+
|Val| *----> |Val| *----> |Val|   |---> NULL
+---+---+    +---+---+    +---+---+
Head         Node 1       Node 2

```

---

### 2. Contiguous Memory Representation (Arrays)

An array stores all elements in a single contiguous block of main memory.

#### Random Access Math

Because elements are stored contiguously, the physical memory address of the $i$-th element is computed directly using an offset calculation:


$$\text{Address}(A[i]) = \text{Base Address} + (i \times \text{Element Size})$$

* **Lookup Time:** $\mathcal{O}(1)$ (Constant Time) — The CPU directly jumps to the calculated memory address regardless of whether $i$ is at the beginning, middle, or end of the array.

#### Insertion and Deletion Overhead

* **Insertion at Index $i$:** Requires shifting all subsequent elements from index $i$ to $n-1$ one position to the right to create space.
* **Deletion at Index $i$:** Requires shifting all elements from index $i+1$ to $n-1$ one position to the left to close the gap.
* **Worst-Case Time:** $\mathcal{O}(n)$ — When inserting or deleting at the beginning (index $0$), all $n$ elements must be shifted.

---

### 3. Pointer-Based Representation (Linked Lists)

A linked list allocates memory dynamically for each node containing a data value and a pointer/reference to the next node. Nodes can be scattered anywhere in heap memory.

#### Access Overhead

To reach the $i$-th element, the algorithm must sequentially traverse pointers starting from the `Head` node:


$$\text{Head} \to \text{Node}_1 \to \text{Node}_2 \to \dots \to \text{Node}_i$$

* **Lookup Time:** $\mathcal{O}(n)$ — Accessing arbitrary element $i$ takes linear time proportional to $i$.

#### Structural Modification (In-Place Pointer Swap)

Given a direct pointer to the target node location:

* **Insertion/Deletion:** Performed locally by reassigning $1$ or $2$ pointers.
* **Modification Time (at known location):** $\mathcal{O}(1)$.
* **Overall Time (from Head):** $\mathcal{O}(n)$ because traversing to the insertion/deletion location takes $\mathcal{O}(n)$ time.

---

### 4. Algorithmic Trade-Offs & Operations Comparison

| Operation | Array (Contiguous) | Linked List (Pointers) | Primary Bottleneck / Cause |
| --- | --- | --- | --- |
| **Lookup ($A[i]$)** | $\mathbf{\mathcal{O}(1)}$ | $\mathcal{O}(n)$ | Offset math vs. sequential traversal |
| **Search (Unsorted)** | $\mathcal{O}(n)$ | $\mathcal{O}(n)$ | Linear scan required in both structures |
| **Insert/Delete (Start)** | $\mathcal{O}(n)$ | $\mathbf{\mathcal{O}(1)}$ | Array requires shifting $n$ elements; List updates head pointer |
| **Insert/Delete (Known Node)** | $\mathcal{O}(n)$ | $\mathbf{\mathcal{O}(1)}$ | Array requires element shifts; List performs local pointer updates |
| **Insert/Delete (By Index)** | $\mathcal{O}(n)$ | $\mathcal{O}(n)$ | Array shifts elements; List traverses to index |

---

### 5. Algorithmic Impact on Search Paradigms

Data layout directly restricts which searching algorithms can be applied:

* **Binary Search ($\mathcal{O}(\log n)$):**
* **Array Requirements:** Requires array representation because computing the midpoint $M = \lfloor (L + R) / 2 \rfloor$ requires $\mathcal{O}(1)$ random access to $A[M]$.
* **Linked List Incompatibility:** Searching a sorted linked list via binary search collapses back to $\mathcal{O}(n)$ time because traversing to the midpoint node $M$ takes $\mathcal{O}(n)$ time per step.


* **Linear Search ($\mathcal{O}(n)$):**
* Operates identically on both arrays and linked lists by traversing elements sequentially.

---


## Lecture Notes: Searching in an Array

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Searching in an Array

---

### 1. Fundamental Search Problem Parameters

The search problem asks whether a target value $K$ exists within a sequence $A$ of length $n$.

Two core conditions affect algorithm design and time complexity:

1. **Data Structure Used:** Contiguous Array ($\mathcal{O}(1)$ random index access) vs. Linked List ($\mathcal{O}(n)$ sequential pointer traversal).
2. **Order of Elements:** Unsorted (random sequence) vs. Sorted (monotonically non-decreasing order).

---

### 2. Unsorted Search (Linear Search)

When elements in $A$ are stored in no specific order, every element must be inspected sequentially because $K$ could reside at any position.

```text
Unsorted Array Scan:
+------+------+------+------+------+------+
| A[0] | A[1] | A[2] | ...  | A[i] |A[n-1]|  <-- Check elements sequentially
+------+------+------+------+------+------+

```

#### Algorithm Steps & Mechanics

* Iterate from index $i = 0$ to $n - 1$.
* If $A[i] == K$, return index $i$ (Found).
* If loop finishes without matching $K$, return `-1` (Not Found).

#### Complexity Metrics

* **Best Case:** $\mathcal{O}(1)$ — $K$ is at index $0$.
* **Worst Case:** $\mathcal{O}(n)$ — $K$ is absent or at index $n-1$.
* **Data Structure Comparison:** Linear search runs in $\mathcal{O}(n)$ time on **both** contiguous arrays and linked lists.

---

### 3. Sorted Search in Arrays (Binary Search)

If array $A$ is sorted in ascending order, binary search eliminates half of the remaining search space with each comparison by checking the midpoint element.

```text
Binary Search Interval Reduction:
Interval:  [L ---------------- Mid ---------------- R-1]
If K < A[Mid]: Search left half  ---> [L ------- Mid-1]
If K > A[Mid]: Search right half ---> [Mid+1 ---- R-1]

```

#### Algorithm Implementation

```text
BinarySearch(A, K, L, R):
    if L == R:                      // Search space is empty
        return False
    
    mid = (L + R) // 2              // Compute middle index
    
    if A[mid] == K:
        return True
    else if K < A[mid]:
        return BinarySearch(A, K, L, mid)       // Search left half [L, mid-1]
    else:
        return BinarySearch(A, K, L, mid + 1, R)  // Search right half [mid+1, R-1]

```

---

### 4. Mathematical Complexity & Recurrence Analysis

#### Recurrence Relation

Let $T(n)$ be the time taken to search an array segment of size $n$:

* **Base Case:** $T(0) = 1$
* **Recurrence Step:** $T(n) = 1 + T\left(\frac{n}{2}\right)$
*(where $1$ is constant time to calculate `mid` and compare $A[\text{mid}]$ with $K$)*.

#### Unwinding / Substitution Method

1. $T(n) = 1 + T\left(\frac{n}{2}\right)$
2. $T(n) = 1 + 1 + T\left(\frac{n}{2^2}\right) = 2 + T\left(\frac{n}{2^2}\right)$
3. $T(n) = 3 + T\left(\frac{n}{2^3}\right)$
4. After $k$ steps: $T(n) = k + T\left(\frac{n}{2^k}\right)$

Setting $\frac{n}{2^k} = 1 \implies 2^k = n \implies k = \log_2(n)$:


$$T(n) = \log_2(n) + T(1) = \log_2(n) + 1 + T(0) = \mathbf{\mathcal{O}(\log_2 n)}$$




---

### 5. Why Binary Search Fails on Linked Lists

Binary Search achieves $\mathcal{O}(\log n)$ time **only** if computing and accessing $A[\text{mid}]$ takes $\mathcal{O}(1)$ time.

* **Arrays:** Computing $\text{mid} = \frac{L + R}{2}$ gives instant $\mathcal{O}(1)$ access to $A[\text{mid}]$ via memory offset.
* **Linked Lists:** Finding the midpoint node requires traversing $\frac{n}{2}$ pointers, taking $\mathcal{O}(n)$ time per step.
* **Recurrence on Linked List:**

$$T(n) = \mathcal{O}(n) + T\left(\frac{n}{2}\right) \implies \mathbf{\mathcal{O}(n)}$$




Finding the midpoint in a list takes linear time, destroying the performance benefit of binary search.

---

### 6. Summary Comparison Matrix

| Property | Linear Search | Binary Search |
| --- | --- | --- |
| **Prerequisite** | None (Unsorted or Sorted) | Elements must be sorted |
| **Supported Structures** | Arrays and Linked Lists | Arrays only ($\mathcal{O}(1)$ indexing required) |
| **Worst-Case Time** | $\mathcal{O}(n)$ | $\mathbf{\mathcal{O}(\log_2 n)}$ |
| **Inspections for $n = 1000$** | Up to $1000$ comparisons | At most $\approx 10$ comparisons ($\log_2 1024 = 10$) |



---



## Lecture Notes: Selection Sort

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Selection Sort

---

### 1. Motivation for Sorting

Sorting reorders a sequence into a structured (ascending or descending) order. It serves as a foundational step for many downstream algorithms:

* **Searching:** Enables $\mathcal{O}(\log n)$ binary search instead of $\mathcal{O}(n)$ linear search.
* **Statistical Operations:** Median element identification becomes instant ($\mathcal{O}(1)$ at index $\lfloor n/2 \rfloor$).
* **Duplicate Removal & Frequency Count:** Equal values form contiguous blocks, enabling a single-pass $\mathcal{O}(n)$ scan to count or deduplicate elements.

---

### 2. Selection Sort Core Mechanics

Selection Sort operates by repeatedly finding (selecting) the minimum element from the unsorted portion of the array and placing it in its correct sorted position.

```text
Iterative In-Place Swapping Execution:
Initial:     [74 | 32 | 89 | 55 | 64 | 21]   -> Min in unsorted is 21 (swap with index 0)
Pass 1:      [21 | 32 | 89 | 55 | 64 | 74]   -> Min in [32..74] is 32 (already at index 1)
Pass 2:      [21 | 32 | 89 | 55 | 64 | 74]   -> Min in [89..74] is 55 (swap with index 2)
Pass 3:      [21 | 32 | 55 | 89 | 64 | 74]   -> Min in [89..74] is 64 (swap with index 3)
Pass 4:      [21 | 32 | 55 | 64 | 89 | 74]   -> Min in [89..74] is 74 (swap with index 4)
Pass 5:      [21 | 32 | 55 | 64 | 74 | 89]   -> Sorted!
             |_______Sorted_______|Unsorted|

```

* **In-Place Optimization:** Instead of creating an auxiliary output array ($\mathcal{O}(n)$ extra space), swap the minimum element found with the element at the boundary index of the unsorted segment.

---

### 3. Iterative Implementation

```text
SelectionSort_Iterative(A, n):
    for start = 0 to n - 1:
        min_pos = start
        for j = start + 1 to n - 1:
            if A[j] < A[min_pos]:
                min_pos = j
        
        // Swap minimum element to its correct position
        swap(A[start], A[min_pos])
```

---

### 4. Recursive Implementation

The algorithm can be naturally formulated recursively by identifying the prefix element and making a recursive call on the remaining suffix:

```text
SelectionSort_Recursive(A, start, n):
    if start >= n - 1:
        return                          // Base case: 1 or 0 elements remaining
    
    min_pos = start
    for j = start + 1 to n - 1:
        if A[j] < A[min_pos]:
            min_pos = j
    
    swap(A[start], A[min_pos])
    
    // Recursive call on reduced unsorted sub-array
    SelectionSort_Recursive(A, start + 1, n)
```

---

### 5. Time & Space Complexity Analysis

#### Iterative Analysis
Finding the minimum element in an unsorted sub-array of length $k$ requires $k - 1$ comparisons. Summing comparisons across all passes:
$$T(n) = n + (n - 1) + (n - 2) + \dots + 1 = \sum_{i=1}^{n} i = \frac{n(n + 1)}{2} = \mathbf{\mathcal{O}(n^2)}$$


#### Recursive Recurrence Relation
* **Base Case:** $T(1) = 1$
* **Recurrence Step:** $T(n) = n + T(n - 1)$

Unwinding the recurrence via substitution:
1. $T(n) = n + T(n - 1)$
2. $T(n) = n + (n - 1) + T(n - 2)$
3. $T(n) = n + (n - 1) + (n - 2) + \dots + 1 = \mathbf{\mathcal{O}(n^2)}$

---

### 6. Summary Properties Table

| Metric | Property Value | Reason / Explanation |
| :--- | :--- | :--- |
| **Worst-Case Time** | $\mathcal{O}(n^2)$ | Scans all remaining elements to find minimum regardless of initial order. |
| **Best-Case Time** | $\mathcal{O}(n^2)$ | Always performs $n(n-1)/2$ comparisons even if array is already sorted. |
| **Average-Case Time** | $\mathcal{O}(n^2)$ | Sum of arithmetic sequence $\sum_{1}^{n} i$ dominates. |
| **Space Complexity** | $\mathcal{O}(1)$ (Iterative) | Operates strictly in-place via element swapping. |
| **Swaps Complexity** | $\mathcal{O}(n)$ | Exactly $n - 1$ swap operations are performed in total. |


---


## Lecture Notes: Insertion Sort

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Insertion Sort

---

### 1. Conceptual Mechanics & Card Player Analogy

Insertion Sort builds a sorted array element-by-element by repeatedly picking the next unsorted element and inserting it into its correct position within the already sorted prefix.

```text
Insertion Sort Execution Trace:
Initial Array: [74 | 32 | 89 | 55 | 21 | 64]
-----------------------------------------------------------------------
Pass 1 (k=1): [74 | 32] 89  55  21  64  -> Swap 32 with 74
              [32   74] 89  55  21  64  (Sorted prefix size 2)

Pass 2 (k=2): [32   74 | 89] 55  21  64  -> 89 > 74, stays in place
              [32   74   89] 55  21  64  (Sorted prefix size 3)

Pass 3 (k=3): [32   74   89 | 55] 21  64  -> Shift 89, 74 right; insert 55
              [32   55   74   89] 21  64  (Sorted prefix size 4)

Pass 4 (k=4): [32   55   74   89 | 21] 64  -> Shift all left elements right
              [21   32   55   74   89] 64  (Sorted prefix size 5)

Pass 5 (k=5): [21   32   55   74   89 | 64] -> Shift 89, 74 right; insert 64
              [21   32   55   64   74   89] -> Fully Sorted Array!
```

* **Analogy:** Identical to how a person sorts a hand of playing cards—picking one card at a time and sliding it into its correct ordered spot.

---

### 2. Iterative Implementation

```text
InsertionSort_Iterative(A, n):
    for i = 1 to n - 1:
        curr = i
        // Shift element left as long as left neighbor is larger
        while curr > 0 and A[curr] < A[curr - 1]:
            swap(A[curr], A[curr - 1])
            curr = curr - 1
```

---

### 3. Recursive Formulation

```text
InsertionSort_Recursive(A, start, n):
    if start >= n:
        return                          // Base Case: Processed all elements
    
    Insert(A, start)                    // Insert A[start] into A[0..start-1]
    InsertionSort_Recursive(A, start + 1, n)

Insert(A, pos):
    while pos > 0 and A[pos] < A[pos - 1]:
        swap(A[pos], A[pos - 1])
        pos = pos - 1
```

---

### 4. Binary Search Ineffectiveness for Insertion Optimization

* **Optimization Hypothesis:** Using Binary Search to locate the target insertion index in the sorted sub-array $A[0..k-1]$ takes logarithmic time $\mathcal{O}(\log k)$.
* **The Shifting Bottleneck:** Even after finding the insertion point in $\mathcal{O}(\log k)$ time, physically creating space requires shifting up to $k$ elements one position right.
* **Overall Impact:** Shifting elements still requires linear time $\mathcal{O}(k)$ per step, keeping total time complexity at $\mathcal{O}(n^2)$.

---

### 5. Mathematical Complexity & Recurrence Analysis

#### Worst-Case Analysis (Reverse Sorted Array)
In the worst case (array sorted in reverse order), inserting element $A[k]$ into $A[0..k-1]$ requires $k$ swap steps:
$$T(n) = \sum_{k=1}^{n-1} k = 1 + 2 + 3 + \dots + (n - 1) = \frac{n(n - 1)}{2} = \mathbf{\mathcal{O}(n^2)}$$


#### Recursive Recurrence Relation
* **Base Case:** $T(1) = 1$
* **Recurrence Step:** $T(n) = T(n - 1) + (n - 1)$ *(where $n-1$ represents the worst-case insertion phase)*
$$T(n) = T(n - 2) + (n - 2) + (n - 1) = \mathbf{\mathcal{O}(n^2)}$$


#### Best-Case Analysis (Already Sorted Array)
* If the array is already sorted, the `while` loop condition `A[curr] < A[curr-1]` evaluates to `False` on the very first test for every element $i$.
* Exactly $1$ comparison is performed per element with zero swaps.
* **Best-Case Complexity:** $\mathbf{\mathcal{O}(n)}$ (Linear Time).

---

### 6. Comparison: Selection Sort vs. Insertion Sort vs. Bubble Sort

| Algorithm | Worst-Case Time | Best-Case Time | Swaps Complexity | Adaptive to Pre-Sorted Data? |
| :--- | :--- | :--- | :--- | :--- |
| **Selection Sort** | $\mathcal{O}(n^2)$ | $\mathcal{O}(n^2)$ | $\mathbf{\mathcal{O}(n)}$ | No (Always scans full array) |
| **Insertion Sort** | $\mathcal{O}(n^2)$ | $\mathbf{\mathcal{O}(n)}$ | $\mathcal{O}(n^2)$ worst / $0$ best | **Yes** (Runs in $\mathcal{O}(n)$ time for nearly sorted arrays) |
| **Bubble Sort** | $\mathcal{O}(n^2)$ | $\mathcal{O}(n)$ (optimized) | $\mathcal{O}(n^2)$ | Yes (if modified with swap-flag check) |



---


## Lecture Notes: Merge Sort (Concept & Implementation)

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Merge Sort

---

### 1. Conceptual Mechanics & Divide and Conquer Strategy

Merge Sort breaks away from the $\mathcal{O}(n^2)$ complexity bound of Selection and Insertion Sort by applying the **Divide and Conquer** paradigm:

```text
               [43, 32, 22, 78, 63, 57, 91, 13]         <-- DIVIDE (Split in half)
                            /        \
             [43, 32, 22, 78]        [63, 57, 91, 13]
               /        \              /        \
         [43, 32]    [22, 78]    [63, 57]    [91, 13]

```



## Lecture Notes: Merge Sort — Analysis & Applications

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Merge Sort: Analysis

---

### 1. Merge Operation Complexity

To analyze Merge Sort, we first determine the time complexity of the core $Merge(A, B, C)$ subroutine, which combines two sorted sub-arrays $A$ (size $m$) and $B$ (size $n$) into a single sorted array $C$ (size $m+n$).

```text
       Array A (size m)         Array B (size n)
      +---+---+---+---+        +---+---+---+---+
      | 1 | 4 | 7 | 9 |        | 2 | 3 | 6 | 8 |
      +---+---+---+---+        +---+---+---+---+
        ^                        ^
        i                        j
        \                        /
         \                      /
          +---+---+---+---+---+---+---+---+
          | 1 | 2 | 3 | 4 | 6 | 7 | 8 | 9 |   --> Array C (size m + n)
          +---+---+---+---+---+---+---+---+
            ^
            k

```

#### Step-by-Step Step Count

* Every loop iteration copies **exactly one** element into output array $C$ and increments pointer $k$.
* The total number of loop iterations is strictly bounded by $m + n$.
* Within each iteration, a constant number of operations ($\le 7$ basic steps like indexing, comparisons, assignment, and pointer increments) are performed.
* **Time Complexity:**

$$T_{\text{merge}}(m, n) = \mathcal{O}(m + n) = \mathbf{\mathcal{O}(n)} \quad (\text{Linear Time, assuming } m \approx n)$$



---

### 2. Recurrence Relation & Solving via Expansion Method

For an array of size $n$, Merge Sort divides the array into two sub-arrays of size $n/2$, recursively sorts each, and merges them in $\mathcal{O}(n)$ time.

#### Recurrence Equation

* **Base Case:** $T(1) = 1$
* **General Case:** $T(n) = 2 \cdot T\left(\frac{n}{2}\right) + n$  *(Assuming $n = 2^k$ for simplification)*

#### Algebraic Unwinding (Step-by-Step)

1. **Initial Statement:**

$$T(n) = 2 \cdot T\left(\frac{n}{2}\right) + n$$




2. **First Expansion (substitute $T(n/2) = 2 \cdot T(n/4) + n/2$):**

$$T(n) = 2 \cdot \left[ 2 \cdot T\left(\frac{n}{2^2}\right) + \frac{n}{2} \right] + n = 2^2 \cdot T\left(\frac{n}{2^2}\right) + 2n$$




3. **Second Expansion (substitute $T(n/2^2) = 2 \cdot T(n/2^3) + n/2^2$):**

$$T(n) = 2^2 \cdot \left[ 2 \cdot T\left(\frac{n}{2^3}\right) + \frac{n}{2^2} \right] + 2n = 2^3 \cdot T\left(\frac{n}{2^3}\right) + 3n$$




4. **General Form after $j$ Steps:**

$$T(n) = 2^j \cdot T\left(\frac{n}{2^j}\right) + j \cdot n$$




5. **Reaching Base Case ($n / 2^j = 1 \implies j = \log_2 n$):**

$$T(n) = 2^{\log_2 n} \cdot T(1) + (\log_2 n) \cdot n$$


$$T(n) = n \cdot 1 + n \log_2 n = \mathbf{\mathcal{O}(n \log_2 n)}$$





---

### 3. Practical Impact: $\mathcal{O}(n^2)$ vs. $\mathcal{O}(n \log n)$ Scale

Assuming a standard desktop processor running at $10^8$ operations per second:

| Algorithm Class | Complexity | Feasible Maximum Input ($n$) | Time Required for $n = 10^7$ |
| --- | --- | --- | --- |
| **Quadratic Sorts** (Insertion/Selection) | $\mathcal{O}(n^2)$ | $n \approx 10,000$ | $\approx 11.5$ days |
| **Linearithmic Sorts** (Merge Sort) | $\mathbf{\mathcal{O}(n \log n)}$ | $\mathbf{n \approx 10,000,000}$ | $\mathbf{\approx 2.3 \text{ seconds}}$ |

---

### 4. Extending Merge Beyond Sorting (Set Operations)

Because both input lists are sorted, the merge algorithm can be modified to perform set operations in $\mathcal{O}(m+n)$ linear time:

#### A. Set Union (Deduplicated Merge)

* If $A[i] < B[j]$, append $A[i]$ to $C$ and increment $i, k$.
* If $A[i] > B[j]$, append $B[j]$ to $C$ and increment $j, k$.
* If $A[i] == B[j]$, append **only one copy** of $A[i]$ to $C$, and increment **all three** pointers ($i, j, k$).

#### B. Set Intersection

* If $A[i] < B[j]$, increment $i$ (drop $A[i]$).
* If $A[i] > B[j]$, increment $j$ (drop $B[j]$).
* If $A[i] == B[j]$, append $A[i]$ to $C$, and increment $i, j, k$.

#### C. Set Difference ($A \setminus B$)

* If $A[i] < B[j]$, append $A[i]$ to $C$, and increment $i, k$.
* If $A[i] > B[j]$, increment $j$ without writing to $C$.
* If $A[i] == B[j]$, skip the element by incrementing $i$ and $j$ without writing to $C$.

---

### 5. Limitations & Bottlenecks of Merge Sort

1. **Auxiliary Space Overhead ($\mathcal{O}(n)$ Space Complexity):**
* Linear-time merging cannot be performed in-place on arrays without moving elements, which degrades time complexity.
* Sorting requires an extra auxiliary array of total size $n$, increasing memory usage.


2. **Recursive Stack Overhead:**
* Stack frames for $\log_2 n$ call depth add runtime overhead compared to purely iterative algorithms.



---

### 6. Architectural Transition: Motivation for QuickSort

* **Merge Sort Paradigm:** Divide non-restrictively $\to$ Solve sub-problems $\to$ **Heavy Merge Step required**.
* **Alternative Idea (QuickSort):** Partition array into two halves such that **all elements in the left half are $\le$ all elements in the right half**.
* **Advantage:** After recursively sorting both halves, **no merge step is needed**; the combined array is already sorted, eliminating auxiliary space requirements.



---



## Lecture Notes: Quicksort

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Quicksort

---

### 1. Motivation & Core Concept

Quicksort was invented by Tony Hoare (1960) to address Merge Sort's main limitation: **auxiliary memory overhead ($\mathcal{O}(n)$ extra space)**.

```text
MERGE SORT PARADIGM:
Divide non-restrictively -> Recursively Sort -> Combine via Merge (Requires O(n) Extra Memory)

QUICKSORT PARADIGM:
Divide via Pivot Partitioning -> Recursively Sort Left & Right -> No Combination Step Needed!
 (Guarantees Left <= Pivot <= Right)                               (Sorting happens In-Place)

```

* **Ideal Strategy (Median Pivot):** If we could always choose the median element as the pivot, the array would split into two equal halves of size $n/2$.

$$T(n) = 2 \cdot T(n/2) + \mathcal{O}(n) \implies \mathbf{\mathcal{O}(n \log_2 n)}$$


* **The Pivot Selection Paradox:** Finding the exact median in an unsorted array without sorting it first is a non-trivial problem.
* **Practical Solution:** Quicksort picks an easily accessible element (e.g., the first element $A[\text{Left}]$) as the **Pivot $P$**, partitions the remaining elements around $P$, and then recursively sorts the sub-arrays.

---

### 2. High-Level Algorithm Structure

```text
Quicksort(A, Left, Right):
    if (Right - Left) <= 1:
        return                          // Base Case: 0 or 1 element is trivially sorted

    Pivot_Pos = Partition(A, Left, Right)
    
    Quicksort(A, Left, Pivot_Pos)       // Recursively sort elements < Pivot
    Quicksort(A, Pivot_Pos + 1, Right)   // Recursively sort elements > Pivot
```

---

### 3. Partitioning Strategy 1: Single-Direction Sweep (Lomuto Scheme)

Maintains two pointers sweeping from left to right to separate elements into lower ($\le P$) and upper ($> P$) partitions.

```text
Lomuto Partition Memory Layout:
+-------+--------------------+--------------------+-----------------------+
| Pivot |   Elements <= P    |    Elements > P    |   Unpartitioned Data  |
+-------+--------------------+--------------------+-----------------------+
  A[L]    A[L+1 ... Yellow-1]  A[Yellow ... Green-1]   A[Green ... R-1]
```

#### Pointer Invariants
* **Yellow Pointer (`yellow`):** Points to the first element in the upper partition ($> P$).
* **Green Pointer (`green`):** Scans the unpartitioned region.

#### Lomuto Partition Pseudocode
```text
Partition_Lomuto(A, L, R):
    Pivot = A[L]
    yellow = L + 1
    
    for green = L + 1 to R - 1:
        if A[green] <= Pivot:
            swap(A[yellow], A[green])
            yellow = yellow + 1
            
    // Place pivot in its correct final index between partitions
    swap(A[L], A[yellow - 1])
    return (yellow - 1)
```

---

### 4. Partitioning Strategy 2: Two-Pointer Convergent Sweep (Hoare Scheme)

Sweeps simultaneously from both ends of the array toward the center, swapping misplaced elements.

```text
Hoare Partition Convergent Sweep:
+-------+--------------------+--------------------+-----------------------+
| Pivot |   Elements <= P    | Unpartitioned Data |     Elements >= P     |
+-------+--------------------+--------------------+-----------------------+
  A[L]          --->                                         <---
           Yellow (moves right)                         Green (moves left)
```

#### Execution Steps
1. Advance the `yellow` pointer right until finding an element $> P$.
2. Advance the `green` pointer left until finding an element $< P$.
3. Swap `A[yellow]` and `A[green]`.
4. Repeat until the pointers cross (`green < yellow`).
5. Swap the pivot `A[L]` with `A[green]`.

---

### 5. Architectural Comparison: Quicksort vs. Merge Sort

| Property | Merge Sort | Quicksort |
| :--- | :--- | :--- |
| **In-Place Execution** | No ($\mathcal{O}(n)$ auxiliary array needed) | **Yes** ($\mathcal{O}(1)$ auxiliary space beyond call stack) |
| **Heavy Phase** | Heavy **Combine/Merge** phase | Heavy **Divide/Partition** phase |
| **Sub-problem Balance** | Always strictly balanced ($n/2$) | Depends on pivot selection quality |
| **Worst-Case Time** | $\mathcal{O}(n \log n)$ | $\mathcal{O}(n^2)$ *(if pivot is min/max element)* |
| **Average-Case Time** | $\mathcal{O}(n \log n)$ | **$\mathcal{O}(n \log n)$** *(with small constant factors)* |


## Lecture Notes: Quicksort — Analysis & Optimizations

**Course:** Design and Analysis of Algorithms

  

**Source:** NPTEL — Quicksort: Analysis

  

### 1. Recurrence Relations: Best, Worst, and Average Cases

Quicksort's runtime efficiency is entirely governed by the quality of the chosen pivot.

  

Plaintext

```
       BEST CASE (Median Pivot):                      WORST CASE (Extreme Pivot):
              [   Pivot   ]                                   [ Pivot ]
             /             \                                 /         \
       [ Size n/2 ]   [ Size n/2 ]                          []     [ Size n-1 ]
                                                                     /      \
                                                                    []   [ Size n-2 ]
```

#### A. Best-Case Analysis (Median Pivot)

- **Scenario:** The pivot consistently splits the array into two equal halves of size $n/2$.
    
      
    
- **Recurrence Relation:**
    
      
    
    $$T(n) = 2 \cdot T\left(\frac{n}{2}\right) + n$$
    
    
    
      
    
- **Complexity:** $\mathbf{\mathcal{O}(n \log_2 n)}$ (Identical to Merge Sort)
    
      
    

#### B. Worst-Case Analysis (Extreme Pivot: Minimum or Maximum Element)

- **Scenario:** The pivot is always the smallest or largest element (e.g., executing Quicksort with first-element pivot on an **already sorted array** $[1, 2, 3, \dots, n]$).
    
      
    
- **Partition Split:** One sub-array has size $0$, and the other has size $n - 1$.
    
      
    
- **Recurrence Relation:**
    
      
    
    $$T(n) = T(n - 1) + n$$
    
    
    
      
    
- **Expansion:**
    
      
    
    $$T(n) = n + (n - 1) + (n - 2) + \dots + 1 = \frac{n(n + 1)}{2} = \mathbf{\mathcal{O}(n^2)}$$
    
    
    
      
    

### 2. Average-Case & Expected Time Complexity

#### A. Permutation Space Model

- For an array of size $n$, there are $n!$ possible relative orderings (permutations).
    
      
    
- Assuming a **uniform probability distribution**, each permutation has a probability of $\frac{1}{n!}$ of occurring.
    
      
    
- **Expected Running Time:** Averaging over all $n!$ inputs yields an expected time of $\mathbf{\mathcal{O}(n \log_2 n)}$.
    
      
    

#### B. The Randomized Quicksort Solution

To prevent an adversary from constructing a worst-case $\mathcal{O}(n^2)$ input array for a fixed pivot strategy (e.g., always choosing $A[\text{Left}]$), we use a **Randomized Algorithm**:

Plaintext

```
Randomized_Partition(A, Left, Right):
    Random_Idx = Uniform_Random_Choice(Left, Right - 1)
    swap(A[Left], A[Random_Idx])    // Move chosen random element to pivot slot
    return Partition(A, Left, Right)
```

* **Expected Time:** $\mathbf{\mathcal{O}(n \log_2 n)}$ for **any input distribution**, making worst-case triggers exceedingly improbable ($\sim 0$).

---

### 3. Iterative Conversion & Call Stack Optimization

Recursion incurs runtime overhead due to stack frame creation, variable saving, and function suspension/resumes.

```text
Recursive Call Overhead:
+------------------------------------+
|  Context Frame 3 (Left, Right)    |  <-- Pushed to Call Stack
+------------------------------------+
|  Context Frame 2 (Left, Right)    |
+------------------------------------+
|  Context Frame 1 (Left, Right)    |
+------------------------------------+
```

* **Iterative Quicksort:** Since Quicksort does not perform a post-order combination step (unlike Merge Sort), recursive calls can be replaced by an explicit **user-defined Stack** that stores pair coordinates `(Left, Right)`.
* **Stack Depth Optimization:** Always push the **larger** sub-array to the stack first and process the **smaller** sub-array immediately. This guarantees that stack memory usage is strictly bounded by $\mathcal{O}(\log n)$.

---

### 4. Comprehensive Comparison Matrix: Merge Sort vs. Quicksort

| Property | Merge Sort | Quicksort (Standard) | Quicksort (Randomized) |
| :--- | :--- | :--- | :--- |
| **Worst-Case Time** | $\mathcal{O}(n \log n)$ | $\mathcal{O}(n^2)$ | $\mathcal{O}(n^2)$ *(probabilistically negligible)* |
| **Average-Case Time** | $\mathcal{O}(n \log n)$ | $\mathcal{O}(n \log n)$ | $\mathbf{\mathcal{O}(n \log n)}$ |
| **Best-Case Time** | $\mathcal{O}(n \log n)$ | $\mathcal{O}(n \log n)$ | $\mathcal{O}(n \log n)$ |
| **Auxiliary Memory** | $\mathcal{O}(n)$ (Extra Array) | $\mathcal{O}(1)$ (In-Place) | $\mathcal{O}(1)$ (In-Place) |
| **Stack Memory** | $\mathcal{O}(\log n)$ | $\mathcal{O}(n)$ worst / $\mathcal{O}(\log n)$ best | $\mathcal{O}(\log n)$ expected |
| **Practical Speed** | Moderate (High memory copy overhead) | **Extremely Fast** (High cache locality) | **Extremely Fast** (Industry standard) |


---


## Lecture Notes: Sorting — Concluding Remarks

**Course:** Design and Analysis of Algorithms

**Source:** NPTEL — Sorting: Concluding Remarks

---

### 1. Stability in Sorting Algorithms

A sorting algorithm is defined as **stable** if it preserves the relative original order of elements that have equal key values .

```text
ORIGINAL LIST (Sorted alphabetically by name):
[ (Ashwin, 68),  (Chandar, 43),  (Deepa, 68) ]
   ^                               ^
   Both have the same score (68). Ashwin appears BEFORE Deepa.

AFTER STABLE SORT (By Marks Descending):
[ (Ashwin, 68),  (Deepa, 68),   (Chandar, 43) ]  --> Ashwin STILL appears before Deepa. (Stable)

AFTER UNSTABLE SORT (By Marks Descending):
[ (Deepa, 68),   (Ashwin, 68),  (Chandar, 43) ]  --> Order reversed for equal keys! (Unstable)
``` 

#### Why Stability Matters
Stability allows **multi-level key sorting** (e.g., first sorting a dataset by First Name, then performing a stable sort by Last Name guarantees that people with identical last names remain ordered by first name) .

#### Stability Analysis by Algorithm
* **Insertion Sort (STABLE):** Shifts elements through adjacent comparisons . The shift loop stops immediately upon encountering an equal element (`A[curr] < A[curr-1]`), guaranteeing that equal items never cross each other .
* **Merge Sort (STABLE):** During the merge step, if `A[i] == B[j]`, the algorithm explicitly picks the element from the **left** sub-array `A[i]` first, preserving original ordering .
* **Selection Sort (UNSTABLE):** Long-distance swaps can jump the selected minimum element over equal elements residing in intermediate positions .
* **Quicksort (UNSTABLE):** Partitioning swaps elements across long distances around a pivot, causing equal elements to jump over each other .

---

### 2. Physical Data Movement & Hardware Memory Locality

Beyond mathematical comparison bounds, real-world execution speed depends heavily on physical data movement and hardware cache access patterns :

1. **Element Swaps vs. Element Comparisons:**
   * **Selection Sort:** Performs $\mathcal{O}(n^2)$ comparisons but strictly at most $\mathcal{O}(n)$ swaps . Useful when writing to physical memory is extremely expensive (e.g., EEPROM/Flash writes).
   * **Insertion Sort:** Performs $\mathcal{O}(n^2)$ adjacent swaps, but exhibits outstanding **cache locality** because memory accesses are strictly contiguous .

2. **Sequential Memory Access (External Sorting):**
   * **Merge Sort:** Works by sequentially streaming blocks of memory . This makes Merge Sort the industry standard for **External Sorting** (sorting datasets too large to fit in RAM, stored on Hard Drives/NVMe SSDs) .
   * **Quicksort:** Performs random-access jumps based on pivot comparisons, making it unsuitable for disk-based external sorting .

---

### 3. Comprehensive Summary Matrix of Sorting Algorithms

| Algorithm | Best-Case Time | Average-Case Time | Worst-Case Time | Auxiliary Space | Stable? | Primary Use Case / Practical Reality |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Selection Sort** | $\mathcal{O}(n^2)$  | $\mathcal{O}(n^2)$  | $\mathcal{O}(n^2)$  | $\mathcal{O}(1)$ (In-place)  | **No**  | Minimizes write operations ($\le n$ swaps) . |
| **Insertion Sort** | $\mathbf{\mathcal{O}(n)}$  | $\mathcal{O}(n^2)$  | $\mathcal{O}(n^2)$  | $\mathcal{O}(1)$ (In-place)  | **Yes**  | Small arrays ($n \le 16$) or nearly sorted data . |
| **Merge Sort** | $\mathcal{O}(n \log n)$  | $\mathcal{O}(n \log n)$  | $\mathbf{\mathcal{O}(n \log n)}$  | $\mathcal{O}(n)$  | **Yes**  | External sorting (large disk files) & linked lists . |
| **Quicksort** | $\mathcal{O}(n \log n)$  | $\mathbf{\mathcal{O}(n \log n)}$  | $\mathcal{O}(n^2)$  | $\mathcal{O}(1)$ (In-place)  | **No**  | Default general-purpose in-memory sorting . |

---

### 4. Context-Driven Selection Criteria (Engineering Guidelines)

No single sorting algorithm is universally superior; selection depends on operational context :

```text
                               What is your dataset context?
                                            |
        +-----------------------------------+-----------------------------------+
        |                                   |                                   |
Small / Nearly Sorted              In-Memory Large Array               Large Disk File (External)
        |                                   |                                   |
  INSERTION SORT                      QUICKSORT                             MERGE SORT
 (Linear time O(n),                  (In-place, high cache                (Sequential block reads,
  minimal overhead)                   locality, fast average case)          predictable O(n log n))
``` 

1. **Default In-Memory Systems:** Use **Quicksort** (or hybrid variants like Introsort/Timsort) for standard programming language standard libraries (`std::sort`) due to minimal memory overhead and high cache hits .
2. **Databases & External Storage:** Use **Merge Sort** when sorting large database records or files that exceed RAM capacity, taking advantage of linear disk I/O streaming .
3. **Hybrid Production Sorts (Timsort / Introsort):** Modern language runtimes combine these algorithms:
   * Run **Quicksort / Merge Sort** for large splits.
   * Switch to **Insertion Sort** as soon as sub-array sizes fall below a threshold (e.g., $n \le 16$) to eliminate recursive overhead.

<ElicitationsGroup message="Ready for the next lecture whenever you are:">
  <Elicitation label="Paste next NPTEL lecture link" query="Here is the next NPTEL lecture link: "/>
</ElicitationsGroup
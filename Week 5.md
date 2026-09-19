# Union-Find Data Structure (Array Implementation & Amortized Analysis)

---

### 1. Disjoint Set Union (DSU) Problem Definition

The **Union-Find** data structure maintains a collection of disjoint, non-overlapping dynamic sets over a universe of elements $V = \{1, 2, \dots, n\}$.

#### Supported Operations

* **`Find(u)`:** Returns the unique identifier/label of the set (component) containing element $u$.
* **`Union(u, v)`:** Merges the distinct sets containing elements $u$ and $v$ into a single unified set.

```text
  Initial Partition (n=5):       {1}, {2}, {3}, {4}, {5}
  After Union(1, 2):              {1, 2}, {3}, {4}, {5}
  After Union(3, 4):              {1, 2}, {3, 4}, {5}
  After Union(2, 4):              {1, 2, 3, 4}, {5}

```

---

### 2. Implementation Strategies & Complexities

#### A. Naive Array Implementation

* **Data Structure:** An array `component[1...n]` where `component[i]` stores the component ID of node $i$.
* **`Find(u)`:** Returns `component[u]` $\to \mathcal{O}(1)$ time.
* **`Union(u, v)`:** Scans the *entire* array `component[1...n]` to rename all occurrences of `component[v]` to `component[u]` $\to \mathcal{O}(n)$ time.
* **Worst-Case Cost:** $m$ operations can take $\mathcal{O}(m \cdot n)$ time.

#### B. Weighted Union Heuristic (Merge Smaller into Larger)

* **Data Structure:**
1. `component[1...n]`: Component array.
2. `members[c]`: Explicit list of elements in component $c$.
3. `size[c]`: Number of elements in component $c$.


* **Heuristic:** Always rename elements of the **smaller set** into the label of the **larger set**.
* **Key Insight (Logarithmic Doubling):** Every time an element's component label changes, the size of its new component at least **doubles** ($\text{size}_{\text{new}} \ge 2 \cdot \text{size}_{\text{old}}$).
* **Bound:** An element's set label can change at most **$\log_2 n$ times** before the component encompasses all $n$ elements.

---

### 3. Amortized Complexity Analysis (Weighted Union)

* **Individual Operation Worst-Case:** A single union can still take $\mathcal{O}(n)$ time when merging large sets.
* **Total Cost over $m$ Unions:** Each of the $n$ elements is moved at most $\mathcal{O}(\log n)$ times.

$$\text{Total Time for } m \text{ Unions} = \mathcal{O}(m + n \log n)$$


* **Amortized Time per Union:** **$\mathcal{O}(\log n)$** time across a sequence of operations.

---

### 4. Application to Kruskal's MST Algorithm

Using Weighted Union-Find in Kruskal's algorithm yields an optimal overall runtime:

```text
Kruskal_With_DSU(graph):
    1. Sort all E edges globally                    --> O(E log E) or O(E log V)
    2. Initialize DSU with V singletons             --> O(V)
    3. Loop through sorted edges (u, v):
        if Find(u) != Find(v):                     --> O(1) per check
            Add (u, v) to MST
            Union(u, v)                            --> Amortized O(log V) across all passes

```

#### Final Kruskal Complexity:

$$\mathbf{\mathcal{O}(E \log E + V \log V) = \mathcal{O}((E + V) \log V)}$$

---

### 5. Summary Table

| Implementation | `Find(u)` Time | `Union(u, v)` Single Time | Total Amortized Time ($m$ Unions) |
| --- | --- | --- | --- |
| **Naive Array** | $\mathcal{O}(1)$ | $\mathcal{O}(n)$ | $\mathcal{O}(m \cdot n)$ |
| **Weighted Array (Size Heuristic)** | **$\mathcal{O}(1)$** | $\mathcal{O}(\text{size of smaller set})$ | **$\mathcal{O}(m + n \log n)$** |
| **Tree-based DSU (Path Compression)** | $\mathcal{O}(\alpha(n)) \approx \mathcal{O}(1)$ | $\mathcal{O}(\alpha(n)) \approx \mathcal{O}(1)$ | $\mathcal{O}(m \cdot \alpha(n))$ |



---





# Union-Find Data Structure (Pointer/Tree Implementation & Path Compression)

---

### 1. Motivation: Pointer-Based (Tree) Representation

In the array-based DSU approach, updating a component label during a union requires traversing elements of the smaller set ($\mathcal{O}(\text{size})$).

To achieve a constant-time $\mathcal{O}(1)$ union operation, we represent sets as **inverted trees** using parent pointers:

* Each node $x$ contains a pointer `parent[x]` pointing to its parent in the tree.
* The **root** node of each tree serves as the representative element of that set (`parent[root] == root`).

```text
    Set A Representative: [3]             Set B Representative: [7]
               [3]                                   [7]
              /   \                                 /   \
            [1]   [2]                             [5]   [6]
             |                                     |
            [0]                                   [4]

```

---

### 2. DSU Operations Implementation

#### A. `Find(x)` (Find Root Representative)

Traverse parent pointers upward until reaching the root node where `parent[root] == root`.

#### B. `Union(x, y)` (Union-by-Size / Union-by-Rank)

To attach trees efficiently without increasing tree height unnecessarily:

1. Locate roots $r_x = \text{Find}(x)$ and $r_y = \text{Find}(y)$.
2. If $r_x \neq r_y$, make the root of the **smaller tree** point to the root of the **larger tree**.
3. Update size / rank metadata for the new combined root.

```text
  Before Union(0, 4) with Union-by-Size:
  Size(Root 3) = 4, Size(Root 7) = 4

  After Union(0, 4): Attach Root 7 under Root 3
                       [3]
                    /   |   \
                  [1]  [2]  [7]
                   |        /  \
                  [0]     [5]  [6]
                           |
                          [4]

```

---

### 3. Path Compression Optimization

During a `Find(x)` query, we traverse from node $x$ all the way up to its root $r$.

**Path Compression Insight:** As we backtrack from the root, update the parent pointers of **all visited nodes along the path** to point directly to $r$. Subsequent calls to `Find` for any of these nodes become $\mathcal{O}(1)$.

```text
   Path before Find(4):                     Path after Find(4) with Path Compression:
           [3]                                                [3]
            |                                           /   /   \   \
           [7]                                        [7]  [5]  [4]  [6]
            |
           [5]
            |
           [4]

```

#### Pseudocode for `Find` with Path Compression:

```text
Find(x):
    if parent[x] != x:
        parent[x] = Find(parent[x])    // Path Compression Step
    return parent[x]

```

---

### 4. Complexity Analysis & Inverse Ackermann Function

#### A. Without Path Compression (Union-by-Size/Rank Only)

* **`Union` Time:** $\mathcal{O}(1)$ (attaching one root to another).
* **`Find` Time:** $\mathcal{O}(\log n)$ worst-case height.
* **$m$ Operations Total:** $\mathcal{O}(m \log n)$.

#### B. With Path Compression + Union-by-Size/Rank

Combining both heuristics reduces the amortized cost per operation to nearly constant time:

$$\text{Amortized Time per Operation} = \mathbf{\mathcal{O}(\alpha(n))}$$

Where $\alpha(n)$ is the **Inverse Ackermann Function**:

* $\alpha(n)$ grows extremely slowly; for all practical values of $n$ (up to $n = 2^{2^{65536}}$), $\mathbf{\alpha(n) \le 4}$.
* Therefore, $m$ DSU operations execute in effectively **linear time $\mathcal{O}(m)$**.

---

### 5. Array vs. Pointer-Based DSU Summary

| Feature | Array-Based (Weighted Union) | Pointer-Based (Tree + Path Compression) |
| --- | --- | --- |
| **Data Representation** | Flat Array `component[1..n]` | Parent Pointers `parent[1..n]` |
| **`Find(x)` Single Cost** | $\mathcal{O}(1)$ | $\mathcal{O}(\alpha(n)) \approx \mathcal{O}(1)$ amortized |
| **`Union(x, y)` Single Cost** | $\mathcal{O}(\text{size of smaller set})$ | $\mathcal{O}(1)$ |
| **Sequence of $m$ Operations** | $\mathcal{O}(m + n \log n)$ | $\mathbf{\mathcal{O}(m \cdot \alpha(n)) \approx \mathcal{O}(m)}$ |




---





# Priority Queues: Concept & Trade-offs

---

### 1. Abstract Data Type (ADT) Definition

A **Priority Queue** is an abstract data structure that manages a dynamic set of elements where each element has an associated **priority value**.

#### Core Operations

* **`Insert(item, priority)`:** Adds a new item with its priority to the structure.
* **`DeleteMax()` (or `ExtractMax`):** Identifies, removes, and returns the element with the highest priority. *(Note: A Min-Priority Queue provides `DeleteMin`).*

---

### 2. Naive Implementations (1D Structures)

| Implementation | `Insert` Cost | `DeleteMax` Cost | Strategic Bottleneck |
| --- | --- | --- | --- |
| **Unsorted Array / List** | $\mathcal{O}(1)$ (Append at end) | $\mathcal{O}(N)$ (Linear scan to find max) | Search scan |
| **Sorted Array / List** | $\mathcal{O}(N)$ (Shift elements / insertion) | $\mathcal{O}(1)$ (Pop from front/end) | Maintenance during insertion |

---

### 3. Intermediate 2D Array Optimization ($\sqrt{N}$ Matrix)

To balance insertion and extraction costs without complex structures, store $N$ elements across a 2D matrix of dimensions $\sqrt{N} \times \sqrt{N}$ where **each row is sorted individually**:

```text
Row 0 (Sorted): [ 19 | 15 | 12 |  8 |  3 ]  --> Max in Row 0 = 19
Row 1 (Sorted): [ 22 | 14 | 11 | 10 |  2 ]  --> Max in Row 1 = 22
Row 2 (Sorted): [ 18 | 17 |  9 |  6 |  1 ]  --> Max in Row 2 = 18
Row 3 (Sorted): [ 25 | 20 | 16 |  7 |  4 ]  --> Max in Row 3 = 25  <-- GLOBAL MAX

```

#### Operations Mechanics

* **`Insert`:** Identify a row with space ($\mathcal{O}(1)$) and perform an insertion sort within that row of size $\sqrt{N}$ $\to \mathbf{\mathcal{O}(\sqrt{N})}$.
* **`DeleteMax`:** Since each row is internally sorted, the global maximum must be among the first elements of the $\sqrt{N}$ rows. Scan all row heads ($\mathcal{O}(\sqrt{N})$) and remove it $\to \mathbf{\mathcal{O}(\sqrt{N})}$.

---

### 4. Overview Comparison Across Approaches

| Strategy / Structure | `Insert` Time | `DeleteMax` Time | Total Processing Time for $N$ Elements |
| --- | --- | --- | --- |
| **Unsorted List** | $\mathcal{O}(1)$ | $\mathcal{O}(N)$ | $\mathcal{O}(N^2)$ |
| **Sorted List** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | $\mathcal{O}(N^2)$ |
| **2D Array ($\sqrt{N} \times \sqrt{N}$)** | $\mathcal{O}(\sqrt{N})$ | $\mathcal{O}(\sqrt{N})$ | $\mathcal{O}(N \sqrt{N})$ |
| **Binary Heap (Optimal)** | $\mathbf{\mathcal{O}(\log N)}$ | $\mathbf{\mathcal{O}(\log N)}$ | $\mathbf{\mathcal{O}(N \log N)}$ |



---


# Heaps: Concept, Operations, and Array Implementation

---

### 1. Definition and Fundamental Properties

A **Max-Heap** is a complete binary tree that satisfies two strict properties:

1. **Structural Property (Complete Binary Tree):** Nodes are filled level-by-level from **top to bottom, left to right**. There are no "gaps" in the tree structure.
2. **Value Property (Max-Heap Invariant):** For every node $v$ with children $v_{left}$ and $v_{right}$:

$$val(v) \ge val(v_{left}) \quad \text{and} \quad val(v) \ge val(v_{right})$$



*(Note: A **Min-Heap** flips this condition to $val(v) \le \text{children}$).*

#### Height Bound Theorem

For $N$ elements in a complete binary tree, the height $H$ (number of edges from root to lowest leaf) is tightly bounded:


$$H = \lfloor \log_2 N \rfloor = \mathbf{\mathcal{O}(\log N)}$$

```text
                        Valid Max-Heap (N=7):
                                [ 33 ]
                               /      \
                        [ 24 ]          [ 12 ]
                       /      \        /      \
                   [ 11 ]   [ 10 ]  [ 7 ]    [ 5 ]

   • Level 0: 2^0 = 1 node
   • Level 1: 2^1 = 2 nodes
   • Level 2: 2^2 = 4 nodes
   • Total max nodes for K levels = 2^K - 1

```

---

### 2. Core Operations & Mechanics

#### A. Insertion (`Insert`)

1. Place the new element at the **first empty leaf position** (maintaining CBT shape).
2. **Up-Heap / Sift-Up / Bubble-Up:** Repeatedly swap the element with its parent as long as it violates the heap property ($val(\text{current}) > val(\text{parent})$).
3. **Complexity:** Path length from leaf to root $\to \mathbf{\mathcal{O}(\log N)}$.

#### B. Delete-Max (`ExtractMax`)

1. The maximum element always resides at the **root** ($A[0]$).
2. Replace the root with the **last leaf element** in the heap, then delete that last leaf.
3. **Down-Heap / Sift-Down / Heapify-Down:** Compare the new root with its children. Swap it with the **larger child** if a violation exists. Repeat down the tree until the heap invariant is restored.
4. **Complexity:** Path length from root to leaf $\to \mathbf{\mathcal{O}(\log N)}$.

---

### 3. Array Representation (Zero-Based Indexing)

Because a heap is a complete binary tree, it can be mapped into a 1D array without child/parent pointers:

```text
Array Index:  [  0  |  1  |  2  |  3  |  4  |  5  |  6  ]
Values:       [ 33  | 24  | 12  | 11  | 10  |  7  |  5  ]

```

#### Index Formulae (for node at 0-based index $i$):

* **Left Child:** $\mathbf{\text{Left}(i) = 2i + 1}$
* **Right Child:** $\mathbf{\text{Right}(i) = 2i + 2}$
* **Parent:** $\mathbf{\text{Parent}(i) = \lfloor \frac{i - 1}{2} \rfloor}$

---

### 4. Efficient Heap Building (`Heapify` / Bottom-Up)

#### A. Naive Approach

* Insert $N$ elements one-by-one into an initially empty heap.
* $\mathcal{O}(N \log N)$ total time.

#### B. Optimal Bottom-Up Construction (`Build-Heap`)

1. Treat an unorganized array of $N$ elements directly as a complete binary tree.
2. All leaf nodes (from index $\lfloor N/2 \rfloor$ to $N-1$) trivially satisfy the heap property.
3. Iterate backwards from the last non-leaf node ($\lfloor N/2 \rfloor - 1$) up to the root ($0$), calling `SiftDown` on each node.

#### Mathematical Proof of $\mathcal{O}(N)$ Time:

$$\text{Total Work} = \sum_{h=0}^{\lfloor \log N \rfloor} \frac{N}{2^{h+1}} \cdot \mathcal{O}(h) = \mathcal{O}\left(N \sum_{h=0}^{\infty} \frac{h}{2^h}\right) = \mathbf{\mathcal{O}(N)}$$

---

### 5. Summary Table of Priority Queue Operations

| Operation | Array / Unsorted List | Array / Sorted List | Binary Heap Implementation |
| --- | --- | --- | --- |
| **`Insert`** | $\mathcal{O}(1)$ | $\mathcal{O}(N)$ | $\mathbf{\mathcal{O}(\log N)}$ |
| **`GetMax`** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | $\mathbf{\mathcal{O}(1)}$ |
| **`DeleteMax`** | $\mathcal{O}(N)$ | $\mathcal{O}(1)$ | $\mathbf{\mathcal{O}(\log N)}$ |
| **`BuildHeap`** | — | — | $\mathbf{\mathcal{O}(N)}$ |


---




# Heaps: Updating Values and HeapSort

---

### 1. Updating Values in a Heap (Decrease-Key / Increase-Key)

In graph algorithms like **Dijkstra’s** and **Prim’s**, edge relaxations require modifying the stored key/priority of an element already present inside a Min-Heap.

#### Mechanics of Value Update

1. **Increase Value (in Max-Heap) / Decrease Value (in Min-Heap):**
* The node's priority improves relative to its ancestors.
* **Fix Direction:** **Upward (`UpHeap` / `SiftUp`)**. The node can violate the heap invariant with its parent, so bubble it up toward the root.


2. **Decrease Value (in Max-Heap) / Increase Value (in Min-Heap):**
* The node's priority worsens relative to its descendants.
* **Fix Direction:** **Downward (`DownHeap` / `SiftDown`)**. The node can violate the heap invariant with its children, so push it down toward the leaves.


3. **Time Complexity:** Both repair operations take **$\mathcal{O}(\log N)$** time (proportional to tree height).

---

### 2. Bidirectional Mapping (Locating Nodes in $\mathcal{O}(1)$)

An array-based heap allows parent/child navigation via formulas ($2i+1, 2i+2$), but **finding which index contains a specific graph vertex $v$ takes $\mathcal{O}(N)$ linear scan**.

To overcome this bottleneck, maintain **two auxiliary position-mapping arrays**:

```text
               Graph Vertex IDs (1...N)               Heap Indices (0...N-1)
                      +-------+                           +-------+
    VertexToHeap[v]   |   3   |  -----------------------> | index |  (Locates node in heap in O(1))
                      +-------+                           +-------+
                          ^                                   |
                          |                                   v
                      +-------+                           +-------+
    HeapToVertex[i]   | vertex|  <----------------------- |   8   |  (Tracks which vertex owns heap index)
                      +-------+                           +-------+

```

#### Dual Mapping Rule:

Whenever two heap elements swap positions during `SiftUp` or `SiftDown`, **both mapping arrays must be updated simultaneously**:

```text
Swap(heap[i], heap[j]):
    swap(heap[i], heap[j])
    swap(HeapToVertex[i], HeapToVertex[j])
    VertexToHeap[HeapToVertex[i]] = i
    VertexToHeap[HeapToVertex[j]] = j

```

With this mapping, key updates in Dijkstra/Prim run in **$\mathcal{O}(\log N)$** time instead of $\mathcal{O}(N)$.

---

### 3. HeapSort Algorithm

**HeapSort** is a comparison-based sorting algorithm that uses a Max-Heap to achieve an in-place $\mathcal{O}(N \log N)$ sorting routine.

#### Two-Phase Mechanics:

1. **Phase 1: Build-Heap ($\mathcal{O}(N)$):**
Transform the input array of $N$ unsorted elements into a Max-Heap using bottom-up `Heapify`.
2. **Phase 2: Extract-and-Fill ($\mathcal{O}(N \log N)$):**
* Swap the root (maximum element at $A[0]$) with the last element of the heap ($A[\text{heap\_size} - 1]$).
* Reduce `heap_size` by 1 (placing the max element in its final sorted position at the end of the array).
* Run `SiftDown` on the new root $A[0]$ to restore the Max-Heap property for the remaining sub-array.
* Repeat $N - 1$ times.



```text
   Initial Heap Array:     [ 33 | 24 | 12 | 11 | 10 |  7 |  5 ]   (heap_size = 7)
   
   Step 1: Extract Max 33  [  5 | 24 | 12 | 11 | 10 |  7 | 33 ]   (heap_size = 6, 33 finalized!)
   SiftDown root (5):      [ 24 | 11 | 12 |  5 | 10 |  7 | 33 ]
   
   Step 2: Extract Max 24  [  7 | 11 | 12 |  5 | 10 | 24 | 33 ]   (heap_size = 5, 24 finalized!)
   ... continue until sorted!

```

---

### 4. HeapSort Complexity & Trade-offs Summary

| Attribute            | Complexity / Property     | Notes                                                     |
| -------------------- | ------------------------- | --------------------------------------------------------- |
| **Best-Case Time**   | $\mathcal{O}(N \log N)$   | Always builds and extracts full tree.                     |
| **Worst-Case Time**  | $\mathcal{O}(N \log N)$   | Independent of initial element ordering.                  |
| **Average Time**     | $\mathcal{O}(N \log N)$   | Highly consistent performance.                            |
| **Space Complexity** | $\mathbf{\mathcal{O}(1)}$ | In-place sorting using vacated array slots.               |
| **Stability**        | **Unstable**              | Long-distance swaps disrupt relative order of equal keys. |



---



# Counting Inversions

---

### 1. Problem Formulation & Recommendation Context

* **Application Context:** Used in e-commerce and streaming recommendation systems to measure taste similarity or profile disparity between two users ranking $n$ items (e.g., movies, products).
* **Inversion Definition:** Given an array $A$ of $n$ elements representing a permutation of $\{1, 2, \dots, n\}$, an **inversion** is a pair of indices $(i, j)$ such that:

$$i < j \quad \text{and} \quad A[i] > A[j]$$


* **Inversion Bounds:**
* **Min Inversions:** $0$ (array is already sorted in ascending order $\implies$ identical preferences).
* **Max Inversions:** $\binom{n}{2} = \frac{n(n - 1)}{2} = \mathcal{O}(n^2)$ (array is completely reverse-sorted $\implies$ diametrically opposed preferences).



---

### 2. Algorithmic Approaches

#### A. Naive Brute Force

* Check every pair $(i, j)$ with $i < j$.
* **Time Complexity:** $\mathcal{O}(n^2)$

#### B. Divide and Conquer (Modified MergeSort)

* Divide the array $A$ into two halves: Left $L[1 \dots n/2]$ and Right $R[1 \dots n/2]$.
* **Inversion Classification:**
1. **Left Inversions:** Both indices $i, j \in L$.
2. **Right Inversions:** Both indices $i, j \in R$.
3. **Split / Cross Inversions:** Index $i \in L$ and index $j \in R$.



---

### 3. Split Inversion Counting via Merging

During the combine step of MergeSort, both $L$ and $R$ are already sorted.

* When copying an element $R[j]$ into the merged output before $L[i]$ (i.e., $R[j] < L[i]$):
* Since $L$ is sorted, $R[j]$ is strictly smaller than $L[i]$ **and all remaining elements in $L$ from index $i$ to the end of $L$**.
* **Inversion Count Increment:**

$$\text{Count} \gets \text{Count} + (\text{length}(L) - i)$$





```text
               Left (Sorted L):   [ 3 | 7 | 8 ]     Right (Sorted R):  [ 2 | 5 ]
                                    ^                                    ^
                                  i = 0                                j = 0

               • Compare L[0] (3) vs R[0] (2):
                 Since 2 < 3, extract R[0] (2).
                 Split Inversions added = length(L) - i = 3 - 0 = 3
                 (Pairs formed: (3, 2), (7, 2), (8, 2))

```

---

### 4. Essential Algorithm Pseudocode

```text
MergeAndCount(A, left, mid, right):
    create temporary arrays L = A[left..mid] and R = A[mid+1..right]
    i = 0, j = 0, k = left
    split_inversions = 0

    while i < length(L) and j < length(R):
        if L[i] <= R[j]:
            A[k++] = L[i++]
        else:
            A[k++] = R[j++]
            split_inversions += (length(L) - i)   // Key inversion counting rule!

    while i < length(L):
        A[k++] = L[i++]
    while j < length(R):
        A[k++] = R[j++]

    return split_inversions


SortAndCount(A, left, right):
    if left >= right:
        return 0

    mid = left + (right - left) / 2

    left_inv  = SortAndCount(A, left, mid)
    right_inv = SortAndCount(A, mid + 1, right)
    split_inv = MergeAndCount(A, left, mid, right)

    return left_inv + right_inv + split_inv

```

---

### 5. Time & Space Complexities (Exam Essentials)

* **Recurrence Relation:** $T(n) = 2T(n/2) + \mathcal{O}(n)$
* **Time Complexity:** **$\mathcal{O}(n \log n)$** (Even if there are $\mathcal{O}(n^2)$ inversions, the count is derived implicitly without enumerating each pair individually).
* **Auxiliary Space Complexity:** $\mathcal{O}(n)$ (for temporary arrays during merging).



---



# Closest Pair of Points (Divide & Conquer)

---

### 1. Problem Definition & Mathematical Formulation

Given a set $P$ of $n$ points in a 2D plane $P = \{p_1, p_2, \dots, p_n\}$ where $p_i = (x_i, y_i)$, find a pair of points $(p_i, p_j)$ with $i \neq j$ such that the Euclidean distance $d(p_i, p_j)$ is minimized:

$$d(p_i, p_j) = \sqrt{(x_i - x_j)^2 + (y_i - y_j)^2} \quad \text{}$$

#### Simplifying Assumptions

* To simplify analysis and avoid degenerate boundary edge-cases, assume **no two points share the same x-coordinate or y-coordinate**.

---

### 2. Algorithmic Approaches

| Approach | Mechanics | Time Complexity | Space Complexity |
| --- | --- | --- | --- |
| **Brute Force** | Compare all $\binom{n}{2} = \frac{n(n-1)}{2}$ distinct pairs | $\mathcal{O}(n^2)$ | $\mathcal{O}(1)$ |
| **1D Projection (Warm-up)** | Sort points along the 1D axis; check adjacent elements | $\mathcal{O}(n \log n)$ | $\mathcal{O}(n)$ |
| **2D Divide & Conquer** | Partition plane vertically; check boundary strip efficiently | $\mathbf{\mathcal{O}(n \log n)}$ | $\mathcal{O}(n)$ |

---

### 3. Divide & Conquer Strategy

#### Phase 1: Pre-sorting ($\mathcal{O}(n \log n)$)

Before launching the recursion, create two sorted arrays containing all points:

* $P_x$: Points sorted by ascending **x-coordinate**.
* $P_y$: Points sorted by ascending **y-coordinate**.

#### Phase 2: Splitting & Recursion

1. **Divide:** Find the vertical median line $L$ using the midpoint of $P_x$. Partition $P$ into $Q$ (left subset of size $\lfloor n/2 \rfloor$) and $R$ (right subset of size $\lceil n/2 \rceil$).
2. **Preserve Sorting in Subproblems ($\mathcal{O}(n)$):**
* Divide $P_x$ into $Q_x$ and $R_x$ by splitting at the median.
* Divide $P_y$ into $Q_y$ and $R_y$ by scanning $P_y$ and pushing points to $Q_y$ or $R_y$ based on their x-coordinates relative to $L$.


3. **Conquer:** Recursively compute:

$$\delta_L = \text{ClosestPair}(Q_x, Q_y), \quad \delta_R = \text{ClosestPair}(R_x, R_y) \quad \text{}$$



Let $\delta = \min(\delta_L, \delta_R)$.

```text
                           Left Set Q       |       Right Set R
                                            |
                            •               |          •
                                   •        |     •
                        --------------------+--------------------  Line L
                            •               |          •
                                            |
                         |<----- δ ----->|  |  |<----- δ ----->|
                         |      Strip Zone (Width = 2δ)        |

```

---

### 4. Combining Step: The Boundary Strip ($\mathcal{O}(n)$)

A cross-border pair $(q \in Q, r \in R)$ can only beat the current best $\delta$ if both points lie inside the vertical strip $S$ within distance $\delta$ of line $L$:

$$S = \{ p \in P \mid \vert{}x_p - x_L\vert{} < \delta \} \quad \text{}$$

#### Geometric Sparsity Lemma (The "At Most 15 / 7 Checks" Property)

* Construct $S_y$ by filtering $P_y$ to keep only points in strip $S$ (preserving y-sorted order).
* Divide the strip into grid boxes of dimension $\frac{\delta}{2} \times \frac{\delta}{2}$.
* **Box Capacity:** Each $\frac{\delta}{2} \times \frac{\delta}{2}$ box contains **at most 1 point**, because the maximum distance between any two points in the same box is the diagonal $d_{\text{max}} = \sqrt{(\delta/2)^2 + (\delta/2)^2} = \frac{\delta}{\sqrt{2}} \approx 0.707\delta < \delta$ (which would violate the minimality of $\delta$ within a single partition).
* **Bounded Look-ahead:** For any point $p \in S_y$, we only need to compare its distance against the **next constant number of points (at most 7 to 15 points)** following $p$ in $S_y$.

```text
                        +-----------+-----------+
                        |  Box 1    |  Box 2    |   Each box: (δ/2) x (δ/2)
                        +-----------+-----------+   Max internal distance = δ / √2 < δ
                        |  Box 3    |  Box 4    |   
                        +-----------+-----------+   --> At most 1 point per box!
                        |  Box 5    |  Box 6    |   --> For any point p, check only
                        +-----------+-----------+       a fixed geometric window.

```

---

### 5. Recurrence Relation & Complexity Proof

* **Pre-sorting:** $\mathcal{O}(n \log n)$
* **Recursive Recurrence:**

$$T(n) = 2T\left(\frac{n}{2}\right) + \mathcal{O}(n) \quad \text{}$$


* By Master Theorem (Case 2), $T(n) = \mathbf{\mathcal{O}(n \log n)}$.
* **Overall Time Complexity:** $\mathcal{O}(n \log n) + \mathcal{O}(n \log n) = \mathbf{\mathcal{O}(n \log n)}$.



---





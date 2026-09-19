# Single Source Shortest Paths: Dijkstra's Algorithm

---

### 1. Problem Definition & Core Concept

* **Problem Type:** Single-Source Shortest Path (SSSP) on weighted graphs with non-negative edge weights.
* **Core Intuition:** Physical "oil pipeline / fire propagation" model. Fire spreads at a constant speed from a source node; the exact time a node catches fire equals its shortest distance from the source.
* **Greedy Strategy:** At each step, select the unvisited vertex with the minimum tentative distance, finalize its shortest distance, and relax its outgoing edges.

---

### 2. Time & Space Complexities (Exam Essentials)

| Data Structure / Representation | Time Complexity | Auxiliary Space |
| --- | --- | --- |
| **Array-based (Adjacency Matrix)** | $\mathcal{O}(V^2)$ | $\mathcal{O}(V)$ |
| **Min-Heap / Priority Queue (Adjacency List)** | $\mathbf{\mathcal{O}((V + E) \log V)}$ | $\mathcal{O}(V)$ |
| **Fibonacci Heap (Theoretical Optimal)** | $\mathbf{\mathcal{O}(E + V \log V)}$ | $\mathcal{O}(V)$ |

---

### 3. Essential Algorithm Pseudocode (Edge Relaxation)

```text
Dijkstra(graph, source):
    for each vertex v in graph:
        dist[v] = INFINITY
        visited[v] = false
        parent[v] = -1

    dist[source] = 0

    for i = 1 to V:
        u = vertex with minimum dist[u] among unvisited nodes
        if dist[u] == INFINITY:
            break                   // Remaining nodes unreachable
        
        visited[u] = true

        for each neighbor v of u:
            if not visited[v]:
                // Edge Relaxation Step
                if dist[u] + weight(u, v) < dist[v]:
                    dist[v] = dist[u] + weight(u, v)
                    parent[v] = u

```

---

### 4. Critical Exam Rules & Limitations

1. **Non-Negative Edge Weight Constraint:** Dijkstra's algorithm **FAILS** if the graph contains **negative edge weights** (greedy choice breaks because a future negative edge could reduce an already finalized distance).
2. **Infinity Representation:** Represented computationally as a value strictly greater than the sum of all edge weights in the graph ($\sum w(e) + 1$).
3. **BFS Relation:** Unweighted SSSP (or graph with uniform edge weights) reduces Dijkstra to standard **BFS** with $\mathcal{O}(V + E)$ complexity.


---

# Dijkstra's Algorithm: Analysis & Correctness

---

### 1. Correctness Proof (Greedy Invariant)

Dijkstra's algorithm relies on a **Greedy Strategy**. Its correctness is proved by establishing a loop invariant:

* **Loop Invariant:** At any iteration, for every node $u$ in the "burnt / visited" set $S$, the stored distance $dist[u]$ is the **exact, true shortest path distance** from the source.
* **Inductive Choice:** When adding an unvisited vertex $v$ with minimum tentative distance to $S$, no future path passing through another unvisited vertex $w$ can yield a shorter distance to $v$ because $dist[w] \ge dist[v]$ (assuming non-negative edge weights).

```text
               ( Already Burnt Set S )            ( Unburnt / Unvisited )
              +-----------------------+               +--------------+
              |                       |   w(x,v)      |              |
              |   Source (s) ---> x --+-------------> |   v (Chosen) |
              |                       |               |              |
              |                   y --+-------------> |   w          |
              +-----------------------+   w(y,w)      +--------------+
                                                      
              Since dist[x] + w(x,v) <= dist[y] + w(y,w), no alternative path 
              through w can ever reach v with a smaller distance later!

```

---

### 2. Complexity Breakdown Across Implementations

The overall time complexity depends on how the minimum-distance vertex selection and edge relaxations are handled:

| Implementation | Min-Vertex Selection | Edge Relaxation | Total Time Complexity |
| --- | --- | --- | --- |
| **Adjacency Matrix + Array** | $\mathcal{O}(V)$ per node $\to \mathcal{O}(V^2)$ | $\mathcal{O}(V)$ per node $\to \mathcal{O}(V^2)$ | $\mathbf{\mathcal{O}(V^2)}$ |
| **Adjacency List + Array** | $\mathcal{O}(V)$ per node $\to \mathcal{O}(V^2)$ | $\mathcal{O}(\text{deg}(u)) \to \mathcal{O}(E)$ | $\mathbf{\mathcal{O}(V^2)}$ *(selection is bottleneck)* |
| **Min-Heap / Priority Queue** | $\mathbf{\mathcal{O}(\log V)}$ per node $\to \mathcal{O}(V \log V)$ | $\mathbf{\mathcal{O}(\log V)}$ per edge $\to \mathcal{O}(E \log V)$ | $\mathbf{\mathcal{O}((V + E) \log V)}$ |
| **Fibonacci Heap (Theoretical)** | $\mathcal{O}(\log V)$ amortized | $\mathcal{O}(1)$ amortized | $\mathbf{\mathcal{O}(E + V \log V)}$ |

---

### 3. Impact of Negative Edge Weights & Negative Cycles

#### A. Failure with Negative Edge Weights

* Dijkstra's greedy assumption breaks down completely if negative edges exist.
* **Why?** A path through an unvisited vertex with a higher tentative cost could later encounter a negative edge, reducing the total path cost below an already finalized node's distance.

```text
         [ 2 ] 
        /     \
    10 /       \ -15
      v         v
     (A) ------> (B)
            2

```

*(If $A$ is picked first with distance 10, Dijkstra finalizes $A$. But going $A \to B$ via a negative edge might yield a smaller overall path, which Dijkstra misses).*

#### B. Negative Weight Cycles

* If a graph contains a **negative weight cycle** (a cycle whose total edge weight sum $< 0$), shortest path distance becomes **undefined / $-\infty$** because traversing the cycle infinitely decreases the path cost.
* **Algorithms for Negative Weights:**
1. **Bellman-Ford Algorithm:** Single-Source Shortest Path with negative weights, handles negative cycles in $\mathcal{O}(V \cdot E)$ time.
2. **Floyd-Warshall Algorithm:** All-Pairs Shortest Path in $\mathcal{O}(V^3)$ time.


---

# Negative Edge Weights: Bellman-Ford Algorithm

---

### 1. Key Properties of Shortest Paths (No Negative Cycles)

1. **Path Length Bound:** A shortest path in a graph with $V$ vertices will never contain cycles (assuming no negative cycles). Thus, any simple shortest path contains at most **$V - 1$ edges**.
2. **Optimal Substructure:** Every prefix/sub-path of a shortest path is itself a shortest path between its endpoints.

---

### 2. Core Idea & Mechanism

Dijkstra’s greedy approach fails with negative edge weights because finalized distances can be invalidated later. Bellman-Ford avoids making greedy choices by **relaxing all $E$ edges $V - 1$ times**.

* **Safe Relaxation Property:** Updating a node’s distance via $dist[v] = \min(dist[v], dist[u] + w(u, v))$ never underestimates the true shortest distance.
* **Guarantee:** Running edge relaxations $V - 1$ times ensures that all valid shortest paths of length up to $V - 1$ edges are correctly computed.

---

### 3. Essential Algorithm Pseudocode

```text
BellmanFord(graph, source):
    // Step 1: Initialize distances
    for each vertex v in graph:
        dist[v] = INFINITY
    dist[source] = 0

    // Step 2: Relax all edges (V - 1) times
    for i = 1 to V - 1:
        for each edge (u, v) with weight w in graph:
            if dist[u] != INFINITY and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w

    // Step 3: Check for Negative-Weight Cycles
    for each edge (u, v) with weight w in graph:
        if dist[u] != INFINITY and dist[u] + w < dist[v]:
            return "Graph contains a negative-weight cycle!"

    return dist

```

---

### 4. Detecting Negative-Weight Cycles

* If an edge relaxation is still possible on the $V$-th iteration (after $V - 1$ full passes), the graph **must contain a negative-weight cycle**.

---

### 5. Time & Space Complexities

| Representation | Time Complexity | Auxiliary Space |
| --- | --- | --- |
| **Adjacency Matrix** | $\mathcal{O}(V^3)$ | $\mathcal{O}(V)$ |
| **Adjacency List** | **$\mathcal{O}(V \cdot E)$** | $\mathcal{O}(V)$ |

---

### 6. Comparison: Dijkstra vs. Bellman-Ford

| Feature | Dijkstra's Algorithm | Bellman-Ford Algorithm |
| --- | --- | --- |
| **Negative Edges** | Fails / Not allowed | Allowed |
| **Negative Cycles** | Cannot detect | Detects via $V$-th pass |
| **Time Complexity** | $\mathcal{O}((V + E) \log V)$ | $\mathcal{O}(V \cdot E)$ |
| **Algorithmic Strategy** | Greedy | Dynamic Programming / Iterative Relaxation |


---


# All Pairs Shortest Paths: Floyd-Warshall Algorithm

---

### 1. Problem Definition & Mathematical Formulation

* **Problem Goal:** Find the shortest path distance between **every pair of vertices** $(i, j)$ in a weighted directed graph.
* **Negative Weight Handling:** Works correctly with negative edge weights as long as there are **no negative weight cycles**.
* **Subproblem Definition ($W^k[i][j]$):** The shortest path distance from vertex $i$ to vertex $j$ using only intermediate vertices from the subset $\{1, 2, \dots, k\}$.

---

### 2. Recurrence Relation (Dynamic Programming)

$$\begin{array}{l} \textbf{Base Case (k = 0, Direct Edges Only):} \\ W^0[i][j] = \begin{cases}  0 & \text{if } i = j \\  w(i, j) & \text{if edge } (i, j) \in E \\  \infty & \text{otherwise}  \end{cases} \\ \textbf{Recursive Step (k = 1 to V):} \\ W^k[i][j] = \min \Big( W^{k-1}[i][j], \quad W^{k-1}[i][k] + W^{k-1}[k][j] \Big) \end{array}$$

* **Choice 1:** Do not pass through vertex $k \implies W^{k-1}[i][j]$
* **Choice 2:** Pass through vertex $k \implies W^{k-1}[i][k] + W^{k-1}[k][j]$

---

### 3. Essential Algorithm Pseudocode

```text
FloydWarshall(graph):
    // 1. Initialize Base Case Matrix W[V][V]
    for i = 1 to V:
        for j = 1 to V:
            if i == j:
                W[i][j] = 0
            else if edge (i, j) exists:
                W[i][j] = weight(i, j)
            else:
                W[i][j] = INFINITY

    // 2. Triple Nested Loop (k must be outermost!)
    for k = 1 to V:
        for i = 1 to V:
            for j = 1 to V:
                W[i][j] = min(W[i][j], W[i][k] + W[k][j])

    return W

```

---

### 4. Transitive Closure (Warshall's Variant)

* **Goal:** Determine if a path exists between every pair $(i, j)$ (Reachability / Boolean Matrix).
* **Boolean Operators Replacement:** Replace `min` with `OR` ($\lor$), and `+` with `AND` ($\land$).
* **Recurrence Rule:** $P^k[i][j] = P^{k-1}[i][j] \lor \Big( P^{k-1}[i][k] \land P^{k-1}[k][j] \Big)$

---

### 5. Time & Space Complexities (Exam Highlights)

| Parameter | Complexity | Details / Optimizations |
| --- | --- | --- |
| **Time Complexity** | $\mathbf{\mathcal{O}(V^3)}$ | Driven by 3 nested loops running $1 \dots V$. |
| **Naive Space Complexity** | $\mathcal{O}(V^3)$ | Storing full 3D matrix for all $k$ levels. |
| **Optimized Space Complexity** | $\mathbf{\mathcal{O}(V^2)}$ | Computed in-place using a 2D matrix (oscillating/overwriting levels). |

---

### 6. APSP Strategy Matrix (Which Algorithm to Choose?)

| Graph Type / Condition | Best SSSP Algorithm | All-Pairs Equivalent | APSP Complexity |
| --- | --- | --- | --- |
| **Unweighted Graph** | BFS ($\mathcal{O}(V+E)$) | Run BFS $V$ times | $\mathcal{O}(V^2 + VE)$ |
| **Non-negative Weights** | Dijkstra ($\mathcal{O}(E \log V)$) | Run Dijkstra $V$ times | $\mathcal{O}(V E \log V)$ |
| **Negative Weights (Dense Graph)** | Bellman-Ford ($\mathcal{O}(VE)$) | **Floyd-Warshall** | $\mathbf{\mathcal{O}(V^3)}$ |
| **Negative Weights (Sparse Graph)** | Bellman-Ford ($\mathcal{O}(VE)$) | **Johnson's Algorithm** | $\mathcal{O}(V^2 \log V + VE)$ |



---



# Minimum Cost Spanning Trees (MST)

---

### 1. Fundamental Tree Properties (Exam Essentials)

For any graph $G = (V, E)$ with $n = \vert{}V\vert{}$ vertices, a **Tree** $T$ is a connected, acyclic subgraph.

* **Edge Count Theorem:** Any tree on $n$ vertices contains **exactly $n - 1$ edges**.
* **Cycle Addition Property:** Adding any single edge to a tree forms **exactly one cycle**.
* **Unique Path Property:** There exists **exactly one simple path** between any pair of vertices in a tree.
* **Equivalent Definitions (3-Choose-2 Rule):** Any two of the following properties imply the third:
1. $G$ is connected.
2. $G$ is acyclic.
3. $G$ has $n - 1$ edges.



---

### 2. Spanning Tree & Minimum Spanning Tree (MST)

* **Spanning Tree:** A subgraph that includes **all $n$ vertices** of $G$ and forms a valid tree ($n - 1$ edges connecting all nodes).
* **Minimum Spanning Tree (MST):** A spanning tree $T \subseteq E$ whose total edge weight sum $\sum_{e \in T} w(e)$ is **minimized**.

---

### 3. Core MST Greedy Strategies Overview

```text
                        MST Greedy Strategies
                                  |
        +-------------------------+-------------------------+
        |                                                   |
   Prim's Algorithm                                 Kruskal's Algorithm
 (Grows a single tree)                            (Forest of components)
        |                                                   |
• Starts at a vertex                             • Sorts all edges globally
• Greedily adds cheapest edge                     • Greedily adds cheapest edge
  crossing cut (Tree -> Non-Tree)                   that does NOT form a cycle
• Uses Min-Heap / Priority Queue                 • Uses Disjoint Set Union (DSU)

```

---

### 4. Overview Comparison: Prim's vs. Kruskal's

| Feature | Prim's Algorithm | Kruskal's Algorithm |
| --- | --- | --- |
| **Growth Pattern** | Expands a **single connected tree** outwards | Builds a **forest** that eventually merges |
| **Edge Selection** | Min-weight edge connecting tree to non-tree vertex | Global min-weight edge anywhere in $G$ |
| **Cycle Check** | Inherently avoided by picking unvisited nodes | Explicitly checked via **Union-Find (DSU)** |
| **Primary Data Structure** | Min-Heap / Priority Queue | Disjoint Set Union (DSU) + Edge Sorting |
| **Best For** | **Dense Graphs** ($E \approx V^2$) | **Sparse Graphs** ($E \ll V^2$) |


----



# Prim's Algorithm for Minimum Spanning Trees

---

### 1. The Cut Property (Minimum Separator Lemma)

Prim's algorithm's greedy strategy relies on a fundamental theorem:

* **Cut Definition:** A partition of the vertex set $V$ into two disjoint, non-empty subsets $(U, W)$ such that $U \cup W = V$.
* **Lemma Statement:** For any cut $(U, W)$, the **cheapest edge crossing the cut** (having one endpoint in $U$ and the other in $W$) **must belong to every Minimum Spanning Tree (MST)** (assuming distinct edge weights).
* **Proof Sketch:** If an MST $T$ didn't include this minimum crossing edge $e = (u, w)$, then $T$ must contain some other edge $e'$ crossing the same cut to maintain connectivity. Replacing $e'$ with $e$ yields a new valid spanning tree $T'$ with strictly smaller weight ($\text{weight}(T') < \text{weight}(T)$), contradicting $T$'s minimality.

```text
               Partition U                     Partition W
          (In Current Tree)              (Outside Current Tree)
         +-----------------+            +----------------------+
         |                 |            |                      |
         |    [ Node 1 ]   |            |      [ Node 4 ]      |
         |         |       |  w(2,5)=8  |                      |
         |    [ Node 2 ] --+------------+---> [ Node 5 ]       |
         |                 | (Min Cut)  |                      |
         |    [ Node 3 ]   |            |      [ Node 6 ]      |
         |                 |            |                      |
         +-----------------+            +----------------------+

```

---

### 2. Prim's Algorithm vs. Dijkstra's Algorithm

Prim's algorithm uses a greedy approach similar to Dijkstra's, but with a key difference in edge relaxations:

$$\begin{array}{\|l\|l\|l\|} \hline \textbf{Feature} & \textbf{Dijkstra's Algorithm} & \textbf{Prim's Algorithm} \\ \hline \textbf{Goal} & \text{Single-Source Shortest Paths (SSSP)} & \text{Minimum Spanning Tree (MST)} \\ \hline \textbf{Distance Meaning} & \text{Cumulative path cost from source to } v & \text{Cheapest single edge cost from tree to } v \\ \hline \textbf{Relaxation Rule} & \mathbf{dist[v] = \min(dist[v], dist[u] + w(u, v))} & \mathbf{dist[v] = \min(dist[v], w(u, v))} \\ \hline \textbf{Graph Type} & \text{Directed / Undirected (Non-negative)} & \text{Undirected, Connected} \\ \hline \end{array}$$

---

### 3. Essential Algorithm Pseudocode

```text
Prim_MST(graph, start_vertex):
    // 1. Initialization
    for each vertex v in graph:
        dist[v] = INFINITY
        parent[v] = -1
        visited[v] = false

    dist[start_vertex] = 0

    // Priority Queue stores pairs: (dist[v], v)
    PQ = MinPriorityQueue()
    PQ.insert(0, start_vertex)

    MST_edges = []

    // 2. Main Loop
    while PQ is not empty:
        u = PQ.extract_min()

        if visited[u]:
            continue
        visited[u] = true

        if parent[u] != -1:
            MST_edges.append((parent[u], u))

        // 3. Relax outgoing edges (Cut boundary update)
        for each neighbor v of u with edge weight w(u, v):
            if not visited[v] and w(u, v) < dist[v]:
                dist[v] = w(u, v)
                parent[v] = u
                PQ.insert(dist[v], v)

    return MST_edges

```

---

### 4. Handling Non-Unique Edge Weights

* If edge weights are **not distinct**, multiple valid MSTs with the same total minimum weight can exist.
* **Tie-Breaking Rule:** Order edges strictly using a tuple $(w(e), \text{index}(e))$ to guarantee deterministic execution without affecting correctness.

---

### 5. Time & Space Complexities

| Data Structure / Representation | Time Complexity | Space Complexity |
| --- | --- | --- |
| **Adjacency Matrix + Array** | $\mathcal{O}(V^2)$ | $\mathcal{O}(V)$ |
| **Adjacency List + Min-Heap** | **$\mathbf{\mathcal{O}((V + E) \log V)}$** | $\mathcal{O}(V + E)$ |
| **Fibonacci Heap (Theoretical)** | $\mathbf{\mathcal{O}(E + V \log V)}$ | $\mathcal{O}(V + E)$ |


---



# Kruskal's Algorithm for Minimum Spanning Trees

---

### 1. Core Idea & Forest Growing Strategy

Unlike Prim's algorithm (which expands a single tree outward from a start node), **Kruskal's Algorithm** processes edges globally across the entire graph in ascending order of cost.

* **Forest Approach:** Initially, every vertex belongs to its own isolated component (a forest of $V$ trees).
* **Greedy Edge Addition:** Iteratively inspect the cheapest remaining edge $(u, v)$.
* **No Cycle:** If $u$ and $v$ belong to **different components**, add $(u, v)$ to the MST and merge/union the two components.
* **Cycle Creation:** If $u$ and $v$ already belong to the **same component**, discard the edge (adding it would create a cycle).


* **Termination:** Stop after adding exactly **$V - 1$ edges**.

```text
               Sorted Edges: e1(w=6), e2(w=8), e3(w=10), e4(w=18 - forms cycle!)

  Component A             Component B                  After Union on e3(w=10):
   (1)---(2)               (3)---(4)                      (1)---(2)
      6                       8                              6   \ 10
                                                                  (3)---(4)
                                                                     8

```

---

### 2. High-Level Algorithm & Pseudocode

```text
Kruskal_MST(graph):
    MST_edges = []

    // 1. Sort all edges globally in non-decreasing order of weight
    sort(graph.edges, by=weight)                         // O(E log E)

    // 2. Initialize Disjoint Set Union (DSU) structure
    make_set_for_all_vertices(graph.vertices)             // O(V)

    // 3. Process edges in sorted order
    for each edge (u, v) with weight w in graph.edges:
        if find(u) != find(v):                            // Cycle check: different components?
            MST_edges.append((u, v))
            union(u, v)                                  // Merge the two components

            if length(MST_edges) == V - 1:
                break                                    // MST complete!

    return MST_edges

```

---

### 3. Implementation Variants & Complexities

The performance of Kruskal's algorithm depends on how component connectivity (cycle checking) is tracked:

#### A. Array / Component-ID Vector (Naive)

* Track `component[v]` for every node.
* Checking if $u$ and $v$ are connected: $\mathcal{O}(1)$.
* Merging components: Must update `component[x]` for all nodes in one set $\to \mathcal{O}(V)$ time.
* **Overall Time Complexity:** $\mathcal{O}(E \log E + V \cdot E) = \mathbf{\mathcal{O}(V \cdot E)}$

#### B. Disjoint Set Union (DSU / Union-Find with Path Compression)

* **`find(u)`:** Finds the representative root of $u$'s set (with path compression $\to \alpha(V) \approx \mathcal{O}(1)$ amortized time).
* **`union(u, v)`:** Connects roots by rank/size ($\to \alpha(V)$ amortized time).
* **Overall Time Complexity:** $\mathbf{\mathcal{O}(E \log E)}$ or $\mathbf{\mathcal{O}(E \log V)}$ (since $\log E = \mathcal{O}(\log V)$).

---

### 4. Detailed Comparison: Prim's vs. Kruskal's

| Feature | Prim's Algorithm | Kruskal's Algorithm |
| --- | --- | --- |
| **Strategy** | Grows a **single tree** vertex-by-vertex | Builds a **forest** by globally sorting edges |
| **Primary Data Structure** | Min-Heap / Priority Queue | Disjoint Set Union (DSU) + Sorting |
| **Cycle Prevention** | Keeps track of visited nodes | Uses `find(u) != find(v)` via DSU |
| **Time Complexity** | $\mathcal{O}((V + E) \log V)$ | $\mathcal{O}(E \log E)$ or $\mathcal{O}(E \log V)$ |
| **Best Choice For** | **Dense Graphs** ($E \approx V^2$) | **Sparse Graphs** ($E \ll V^2$) |
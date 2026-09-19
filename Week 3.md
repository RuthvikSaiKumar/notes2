# Breadth First Search (BFS)

---

### 1. Data Structure & Exploration Pattern

* **Core Data Structure:** Queue (First-In, First-Out / FIFO)
* **Exploration Pattern:** Level-by-level
* **Primary Use Case:** Computes the **shortest path** (fewest number of edges) from the source vertex to all reachable vertices in an **unweighted graph**

---

### 2. Time & Space Complexities (Exam Essentials)

| Representation | Time Complexity | Space Complexity |
| --- | --- | --- |
| **Adjacency Matrix** | $\mathcal{O}(V^2)$ or $\mathcal{O}(n^2)$ | $\mathcal{O}(V^2)$ |
| **Adjacency List** | **$\mathcal{O}(V + E)$ or $\mathcal{O}(n + m)$** | $\mathcal{O}(V + E)$ |

* **Input Parameters:** $n = \vert{}V\vert{}$ (number of vertices), $m = \vert{}E\vert{}$ (number of edges)
* **Optimal Complexity:** $\mathcal{O}(V + E)$ using an Adjacency List (considered a linear-time graph algorithm)
* **Edge Exploration Fact:** Every edge $(i, j)$ is examined **at most twice** (once from node $i$, once from node $j$) during an adjacency list traversal

---

### 3. Key Arrays to Remember for Exam Questions

1. `visited[1..n]` (or `level[1..n]`): Tracks visited vertices to prevent re-exploration cycles
2. `parent[1..n]`: Stores the immediate predecessor node to reconstruct the path back to the source
3. `level[1..n]`: Tracks the exact edge distance from the source vertex (`level[source] = 0`, `level[neighbor] = level[current] + 1`)

---

### 4. Essential Pseudocode Mechanics

```text
BFS(graph, source):
    for each vertex u in graph:
        visited[u] = false
        parent[u] = -1
        level[u] = -1

    visited[source] = true
    level[source] = 0
    Q = empty_queue()
    Q.enqueue(source)

    while Q is not empty:
        curr = Q.dequeue()
        for each neighbor v in adj_list[curr]:
            if not visited[v]:
                visited[v] = true
                parent[v] = curr
                level[v] = level[curr] + 1
                Q.enqueue(v)

```



---




# Depth First Search (DFS)

---

### 1. Data Structure & Traversal Order

* **Core Underlying Data Structure:** Stack (Last-In, First-Out / LIFO)
* **Implementation Mechanism:** Recursive Call Stack (implicit stack) or explicit User-Defined Stack
* **Exploration Strategy:** Goes as deep as possible along each branch before backtracking
* **Path Property:** Does **NOT** guarantee shortest paths (unlike BFS)

---

### 2. Time & Space Complexities (Exam Essentials)

| Representation | Time Complexity | Space Complexity |
| --- | --- | --- |
| **Adjacency Matrix** | $\mathcal{O}(V^2)$ or $\mathcal{O}(n^2)$ | $\mathcal{O}(V)$ (call stack depth) |
| **Adjacency List** | **$\mathcal{O}(V + E)$ or $\mathcal{O}(n + m)$** | $\mathcal{O}(V)$ (call stack depth) |

* **Input Parameters:** $n = \vert{}V\vert{}$ (number of vertices), $m = \vert{}E\vert{}$ (number of edges)
* **Linear Time:** $\mathcal{O}(V + E)$ using an Adjacency List

---

### 3. Essential Pseudocode Mechanics

```text
DFS_Init(graph):
    for each vertex u in graph:
        visited[u] = false
        parent[u] = -1
    
    for each vertex u in graph:
        if not visited[u]:
            DFS(u)

DFS(u):
    visited[u] = true
    for each neighbor v in adj_list[u]:
        if not visited[v]:
            parent[v] = u
            DFS(v)                      // Recursive call (Implicit Stack)

```

---

### 4. DFS Numbering: Pre-number and Post-number

DFS augments traversal with a global timestamp counter incremented on entering and exiting a node:

```text
DFS_Numbered(u):
    pre[u] = count++                    // Arrival time
    visited[u] = true
    
    for each neighbor v in adj_list[u]:
        if not visited[v]:
            DFS_Numbered(v)
            
    post[u] = count++                   // Departure time

```

* **Interval Property:** For any two vertices $u$ and $v$, their intervals $[\text{pre}[u], \text{post}[u]]$ and $[\text{pre}[v], \text{post}[v]]$ are either **completely nested** (one is an ancestor of the other) or **completely disjoint**.
* **Primary Structural Uses:**
1. Cycle Detection in Directed and Undirected Graphs
2. Topological Sorting (ordering by descending `post` numbers)
3. Finding Connected / Strongly Connected Components and Cut Vertices (Articulation Points)



---



# Applications of BFS and DFS

---

### 1. Connected Components (Undirected Graphs)

* **Algorithm:** Loop through vertices $1 \dots n$. If vertex $v$ is unvisited, increment component counter (`comp++`) and launch BFS/DFS from $v$.
* **Output:** Labels every vertex with its component ID (`component[u] = comp`).
* **Time Complexity:** $\mathcal{O}(V + E)$ using Adjacency List.

---

### 2. Cycles & Trees (Undirected Graphs)

* **Tree Definition:** Connected, acyclic graph on $V$ vertices with exactly **$E = V - 1$** edges.
* **Cycle Rule:** An undirected graph contains a cycle **if and only if** BFS or DFS encounters a **non-tree edge** (an edge pointing to an already visited vertex).

---

### 3. Edge Classification in Directed Graphs (DFS)

DFS categorizes edges $(u \to v)$ based on the DFS search tree and interval relation between entry (`pre`) and exit (`post`) times:

$$\begin{array}{\|l\|l\|l\|} \hline \textbf{Edge Type} & \textbf{Structural Relation} & \textbf{Interval Property } [\text{pre}, \text{post}] \\ \hline \textbf{Tree Edge} & \text{Edge in the DFS forest} & [\text{pre}[v], \text{post}[v]] \subset [\text{pre}[u], \text{post}[u]] \\ \hline \textbf{Forward Edge} & \text{Ancestor } u \to \text{Descendant } v \text{ (non-tree)} & [\text{pre}[v], \text{post}[v]] \subset [\text{pre}[u], \text{post}[u]] \\ \hline \textbf{Back Edge} & \text{Descendant } u \to \text{Ancestor } v & [\text{pre}[u], \text{post}[u]] \subset [\text{pre}[v], \text{post}[v]] \\ \hline \textbf{Cross Edge} & \text{Between unrelated branches (Right } \to \text{ Left)} & [\text{pre}[u], \text{post}[u]] \cap [\text{pre}[v], \text{post}[v]] = \emptyset \\ \hline \end{array}$$

* **CRITICAL EXAM RULE:** A directed graph contains a cycle **if and only if** DFS reveals at least one **Back Edge**.

---

### 4. Directed Graph Concepts

* **DAG (Directed Acyclic Graph):** A directed graph with **no back edges** (no cycles).
* **Strongly Connected Component (SCC):** Maximal sub-graph where **every vertex is reachable from every other vertex** in both directions ($u \rightsquigarrow v$ and $v \rightsquigarrow u$).
* **Articulation Points / Cut Vertices:** Critical vertices whose removal increases the number of connected components (creates network bottlenecks). Identified using DFS numbers.

---





# Directed Acyclic Graphs (DAGs) & Topological Sort

---

### 1. Fundamental Definitions (Exam Essentials)

* **DAG (Directed Acyclic Graph):** A directed graph containing **no directed cycles**.
* **Topological Order / Sort:** A linear ordering of vertices $v_1, v_2, \dots, v_n$ such that for every directed edge $(u \to v)$, vertex $u$ comes **before** vertex $v$ in the ordering.
* **Cycle Rule:** A directed graph can be topologically sorted **if and only if** it is a **DAG** (has no cycles).
* **Indegree Zero Lemma:** Every finite DAG contains **at least one vertex** with $\text{indegree} = 0$.

---

### 2. Time & Space Complexities

| Representation | Time Complexity | Auxiliary Space |
| --- | --- | --- |
| **Adjacency Matrix** | $\mathcal{O}(V^2)$ | $\mathcal{O}(V)$ |
| **Adjacency List (Kahn's Algorithm - Queue)** | **$\mathcal{O}(V + E)$** | $\mathcal{O}(V)$ |

---

### 3. Kahn's Algorithm Pseudocode (Indegree Removal via Queue)

```text
TopologicalSort_Kahn(graph):
    // 1. Initialize indegrees
    for each vertex u in graph:
        indegree[u] = 0

    for each vertex u in graph:
        for each neighbor v in adj_list[u]:
            indegree[v]++

    // 2. Enqueue all vertices with indegree 0
    Q = empty_queue()
    for each vertex u in graph:
        if indegree[u] == 0:
            Q.enqueue(u)

    // 3. Process Queue
    topo_order = []
    while Q is not empty:
        curr = Q.dequeue()
        topo_order.append(curr)

        for each neighbor v in adj_list[curr]:
            indegree[v]--
            if indegree[v] == 0:
                Q.enqueue(v)

    if length(topo_order) != V:
        return "Cycle detected! Not a DAG"
    return topo_order

```

---

### 4. Alternative Method: DFS-Based Topological Sort

* Run DFS on the DAG and record the **exit/finish (`post`) time** of every vertex.
* **Rule:** Sorting vertices in **descending order of `post` numbers** (or reversing the post-order sequence) yields a valid topological sort.
* **Complexity:** $\mathcal{O}(V + E)$


---

# DAGs: Longest Paths

---

### 1. Problem Formulation & Concept

* **Application Context:** Scheduling tasks/courses with dependency constraints where independent tasks can be run in parallel within the same time block (e.g., semester/day).
* **Core Insight:** The minimum number of time steps required to complete all tasks equals $1 + \text{Length of the Longest Path}$ in the DAG.
* **Paradox / Contrast:**
* Finding the **Shortest Path** in general graphs is easy ($\mathcal{O}(V + E)$).
* Finding the **Longest Simple Path** in *general* graphs is **NP-Hard** (intractable).
* Finding the **Longest Path** in a **DAG** is **Easy** ($\mathcal{O}(V + E)$) because topological ordering guarantees subproblems are resolved in order.



---

### 2. Recurrence Relation & Dynamic Programming Formulation

For any vertex $u$:


$$\text{LongestPath}(u) = \begin{cases} 0 & \text{if } \text{indegree}(u) = 0 \\ 1 + \max_{(v \to u) \in E} \big( \text{LongestPath}(v) \big) & \text{if } \text{indegree}(u) > 0 \end{cases}$$

---

### 3. Integrated Topological Sort & Longest Path Pseudocode

```text
LongestPath_DAG(graph):
    // 1. Initialization
    for each vertex u in graph:
        indegree[u] = 0
        lp[u] = 0                           // Initialize longest path array to 0

    for each vertex u in graph:
        for each neighbor v in adj_list[u]:
            indegree[v]++

    // 2. Queue initialization with indegree 0 nodes
    Q = empty_queue()
    for each vertex u in graph:
        if indegree[u] == 0:
            Q.enqueue(u)

    // 3. Process Queue and maintain Longest Path updates
    while Q is not empty:
        curr = Q.dequeue()

        for each neighbor v in adj_list[curr]:
            // Update DP value for neighbor
            lp[v] = max(lp[v], 1 + lp[curr])
            
            indegree[v]--
            if indegree[v] == 0:
                Q.enqueue(v)

    return max(lp)                          // Returns the longest path length in the DAG

```

---

### 4. Time & Space Complexities

| Representation | Time Complexity | Auxiliary Space |
| --- | --- | --- |
| **Adjacency Matrix** | $\mathcal{O}(V^2)$ | $\mathcal{O}(V)$ |
| **Adjacency List (Kahn's Queue variant)** | **$\mathcal{O}(V + E)$** | $\mathcal{O}(V)$ |


---



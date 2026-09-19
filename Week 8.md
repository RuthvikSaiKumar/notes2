
# Linear Programming

---

### 1. Conceptual Framework & Optimization Problem

**Linear Programming (LP)** is a mathematical optimization framework designed to optimize (maximize or minimize) a **linear objective function**, subject to a set of **linear equality or inequality constraints**.

#### Core Components

1. **Decision Variables:** Continuous variables representing operational choices (e.g., quantities to produce).
2. **Objective Function:** A linear expression to maximize (e.g., profit) or minimize (e.g., cost).
3. **Linear Constraints:** A set of linear inequalities or equalities defining physical, operational, or logical bounds.
4. **Non-Negativity Constraints:** Physical variables usually cannot be negative.

---

### 2. Formulating a Problem: The Sweet Shop Example

Consider optimizing daily production for a sweet shop manufacturing two items: **Barfi ($B$)** and **Halwa ($H$)**.

* **Profit Margins:** ₹100 per box of Barfi, ₹600 per box of Halwa.
* **Demand Limits:** At most 200 boxes of Barfi, at most 300 boxes of Halwa.
* **Production Capacity:** Total staff capacity is capped at 400 boxes per day.

#### Mathematical Formulation

$$\begin{aligned} \text{Maximize Profit: } & Z = 100B + 600H \\ \text{Subject to constraints: } & B \le 200 \quad \text{(Barfi demand)} \\ & H \le 300 \quad \text{(Halwa demand)} \\ & B + H \le 400 \quad \text{(Staff capacity)} \\ & B \ge 0, \, H \ge 0 \quad \text{(Non-negativity)} \end{aligned}$$

---

### 3. Geometric Interpretation & Feasible Region

When plotted in a 2D Cartesian coordinate space ($B$ on the x-axis, $H$ on the y-axis):

1. **Feasible Region:** The intersection of all constraint half-planes forms a bounded, **convex polygon** (trapezium).
2. **Convexity Property:** A region is convex if any line segment connecting two points inside the region lies entirely within the region.
3. **Objective Sweeping Line:** The function $Z = 100B + 600H$ represents a family of parallel lines with slope $-\frac{1}{6}$. Sweeping this line outward increases profit $Z$.

```text
       H (Halwa)
       ^
   300 +------------+-----------* (B=100, H=300) --> OPTIMAL CORNER
       |            |          /  \ (B+H = 400)
       |  FEASIBLE  |         /    \
       |  REGION    |        /      \
       |  (Convex)  |       /        \
     0 +------------+------+----------+-----> B (Barfi)
       0           200    300        400

```

#### Corner Point Theorem (Fundamental Theorem of LP)

An optimal solution to a bounded linear program always occurs at one of the **vertices (corners)** of the convex feasible region.

* **Evaluating Vertices:**
* Corner $(0, 300) \implies Z = 100(0) + 600(300) = \text{₹}1,80,000$
* Corner $(100, 300) \implies Z = 100(100) + 600(300) = \text{₹}1,90,000$ **(Optimal Solution)**
* Corner $(200, 200) \implies Z = 100(200) + 600(200) = \text{₹}1,40,000$



---

### 4. Solution Methods

| Method | Approach | Theoretical Time Complexity | Practical Performance |
| --- | --- | --- | --- |
| **Simplex Algorithm** | Navigates along adjacent vertices of the polytope, continually improving the objective value. | Worst-case exponential $\mathcal{O}(2^n)$ | Extremely efficient on real-world problems. |
| **Ellipsoid Method** | Encloses the feasible region in shrinking ellipsoids. | Polynomial time $\mathcal{O}(n^6)$ | Slow in practice. |
| **Interior-Point Methods (Karmarkar's)** | Traverses through the interior of the convex feasible polytope directly toward the optimal boundary vertex. | Polynomial time $\mathcal{O}(n^{3.5})$ | Highly efficient for massive, large-scale LPs. |

---

### 5. Linear Programming Duality

Every linear program (called the **Primal Problem**) has a corresponding **Dual Problem**.

#### Concept of Duality

* **Primal Goal:** Maximizing profit subject to resource limits.
* **Dual Goal:** Minimizing the total implicit value/cost assigned to resources, ensuring no activity earns more profit than the value of resources it consumes.

#### Strong Duality Theorem

If either the primal or dual problem has an optimal solution, then both have optimal solutions, and the **optimal primal objective value equals the optimal dual objective value**:

$$\max \text{ Profit (Primal)} = \min \text{ Resource Valuation (Dual)}$$

#### Verifying Optimality via Linear Combinations

By taking a valid weighted combination of original constraint inequalities:


$$100 \cdot (H \le 300) + 100 \cdot (B \le 200) + 400 \cdot (B + H \le 400)$$


We mathematically prove that $100B + 600H \le 1,90,000$, providing a tight upper bound certificate for the optimal value.

---

### 6. Algorithmic Complexity

* **Number of Decision Variables:** $n$
* **Number of Constraints:** $m$
* **Linear Programming Time Complexity:** **Polynomial time** $\mathcal{O}(\text{poly}(n, m))$ using Interior-Point methods.



---



# Linear Programming Modeling: Production Planning

---

### 1. Problem Context & Managerial Dilemma

Manufacturing operations frequently face **fluctuating seasonal demand** over a multi-period planning horizon (e.g., 12 months). A plant manager must decide how to meet variable demand without incurring excessive costs.

#### Operational Levers & Associated Costs

1. **Regular Workforce Production:** Regular wages paid to current workers.
2. **Hiring Workers:** Recruitment and onboarding costs per new worker.
3. **Firing Workers:** Severance and administrative costs per laid-off worker.
4. **Overtime Work:** Extra pay per overtime hour/unit produced.
5. **Inventory Holding:** Storage, insurance, and holding costs per unit carried over to the next month.

---

### 2. Decision Variables Formulation

For a planning horizon of $T = 12$ months ($i = 1, 2, \dots, 12$):

* $W_i$: Number of active workers in month $i$.
* $H_i$: Number of workers hired at the start of month $i$.
* $F_i$: Number of workers fired at the start of month $i$.
* $P_i$: Number of units produced in month $i$ using regular hours.
* $O_i$: Number of units produced in month $i$ using overtime.
* $I_i$: Inventory remaining in stock at the end of month $i$.

---

### 3. LP Constraints Formulation

Let $D_i$ be the forecasted demand for month $i$.

#### A. Workforce Balance Equation

The workforce in month $i$ depends on the previous month's workforce, new hires, and layoffs:


$$W_i = W_{i-1} + H_i - F_i \quad \forall i \in \{1, \dots, 12\}$$

#### B. Inventory Balance Equation

The inventory at the end of month $i$ equals previous inventory plus total current production (regular + overtime) minus current demand:


$$I_i = I_{i-1} + P_i + O_i - D_i \quad \forall i \in \{1, \dots, 12\}$$

#### C. Regular Production Capacity Constraint

Assuming each worker can produce up to $k$ units per month during regular working hours:


$$P_i \le k \cdot W_i \quad \forall i \in \{1, \dots, 12\}$$

#### D. Overtime Limit Constraint

Overtime production is strictly capped at a maximum fraction (e.g., $30\%$) of regular production capacity:


$$O_i \le 0.30 \cdot P_i \quad \forall i \in \{1, \dots, 12\}$$

#### E. Non-Negativity Constraints

$$W_i, H_i, F_i, P_i, O_i, I_i \ge 0 \quad \forall i$$

---

### 4. Objective Function: Cost Minimization

The goal is to minimize total operational costs over the 12-month horizon:

$$\text{Minimize } Z = \sum_{i=1}^{12} \Big( c_w W_i + c_h H_i + c_f F_i + c_o O_i + c_i I_i \Big)$$

Where:

* $c_w$: Monthly regular wage per worker.
* $c_h$: Cost to hire a worker.
* $c_f$: Cost to fire a worker.
* $c_o$: Cost per unit produced on overtime.
* $c_i$: Monthly storage cost per unit in inventory.

---

### 5. Fractional Solutions vs. Integer Linear Programming (ILP)

* **Continuous LP Relaxation:** Standard Linear Programming solvers yield continuous (fractional) values (e.g., $W_3 = 24.6$ workers).
* **Rounding Heuristic:** In high-volume production, rounding fractional values (e.g., $24.6 \to 25$ workers) incurs negligible operational cost deviation.
* **Integer Linear Programming (ILP):** Forcing strict integer constraints ($W_i, H_i, F_i \in \mathbb{Z}^+$) transforms a polynomially-solvable Linear Program into an **NP-Hard** problem, making LP relaxation with rounding the standard industry approach.


---

To see how this multi-period production planning model operates in practice, let's walk through a concrete **2-Month Planning Horizon** numerical example.

---

### Problem Scenario

A manufacturing facility has an initial workforce of **10 workers** ($W_0 = 10$) and **0 units of initial inventory** ($I_0 = 0$).

#### Cost Parameters

* **Regular Wage ($c_w$):** ₹20,000 / worker / month
* **Hiring Cost ($c_h$):** ₹5,000 / worker
* **Firing Cost ($c_f$):** ₹8,000 / worker
* **Overtime Cost ($c_o$):** ₹500 / unit
* **Inventory Holding Cost ($c_i$):** ₹100 / unit / month

#### Production Constraints

* Each worker can produce **100 units/month** on regular time ($k = 100$).
* Maximum Overtime is capped at **20%** of regular production ($O_i \le 0.20 \cdot P_i$).
* Forecasted Demand: **Month 1 ($D_1$) = 1,200 units**, **Month 2 ($D_2$) = 800 units**.

---

### Mathematical LP Formulation

#### Decision Variables

* $W_1, W_2$: Active workers in Months 1 and 2.
* $H_1, H_2$: Workers hired at the start of Months 1 and 2.
* $F_1, F_2$: Workers fired at the start of Months 1 and 2.
* $P_1, P_2$: Regular production units in Months 1 and 2.
* $O_1, O_2$: Overtime production units in Months 1 and 2.
* $I_1, I_2$: Ending inventory in Months 1 and 2.

#### Objective Function (Cost Minimization)

$$\text{Minimize } Z = \sum_{i=1}^{2} \Big( 20,000 W_i + 5,000 H_i + 8,000 F_i + 500 O_i + 100 I_i \Big)$$

#### Constraints

**Month 1:**

1. Workforce: $W_1 = 10 + H_1 - F_1$
2. Regular Capacity: $P_1 \le 100 W_1$
3. Overtime Limit: $O_1 \le 0.20 P_1$
4. Inventory Balance: $I_1 = 0 + P_1 + O_1 - 1,200$

**Month 2:**
5. Workforce: $W_2 = W_1 + H_2 - F_2$
6. Regular Capacity: $P_2 \le 100 W_2$
7. Overtime Limit: $O_2 \le 0.20 P_2$
8. Inventory Balance: $I_2 = I_1 + P_2 + O_2 - 800$

---

### Step-by-Step Numerical Solution

#### Month 1 (High Demand: 1,200 units)

* Demand exceeds the capacity of the initial 10 workers ($10 \times 100 = 1,000$ units).
* **Action:** Hire **2 workers** ($H_1 = 2, F_1 = 0$).
* New Workforce: $W_1 = 10 + 2 = 12$ workers.
* Regular Production: $P_1 = 12 \times 100 = 1,200$ units.
* Overtime: $O_1 = 0$ units.
* Ending Inventory: $I_1 = 0 + 1,200 + 0 - 1,200 = 0$ units.



#### Month 2 (Low Demand: 800 units)

* Demand drops to 800 units (requires 8 workers at regular time).
* Laying off 4 workers costs $4 \times 8,000 = \text{₹}32,000$, plus hiring them back later is expensive. Instead, let's compare two strategies for Month 2:
* **Option A (Lay off 4 workers to match exact demand):**
* $F_2 = 4 \implies W_2 = 8$ workers.
* $P_2 = 800$ units, $I_2 = 0$ units.
* *Month 2 Cost:* $8 \times 20,000 \text{ (wages)} + 4 \times 8,000 \text{ (firing)} = \text{₹}1,92,000$.


* **Option B (Maintain 12 workers and build 400 units inventory in Month 1):**
* Requires hiring in Month 1 above demand, increasing inventory holding cost.


* **Option C (Lay off only 2 workers in Month 2 and use inventory/idle capacity):**
* $F_2 = 2 \implies W_2 = 10$ workers ($P_2 = 1,000$ units).
* Production exceeds demand by 200 units $\implies I_2 = 200$ units.
* *Month 2 Cost:* $10 \times 20,000 + 2 \times 8,000 + 200 \times 100 = \text{₹}2,36,000$.



#### Optimal Decision Cost Summary (Option A Strategy)

| Cost Component | Month 1 | Month 2 | Total Cost (₹) |
| --- | --- | --- | --- |
| **Regular Wages ($W_i$)** | $12 \times 20,000 = 2,40,000$ | $8 \times 20,000 = 1,60,000$ | ₹4,00,000 |
| **Hiring Costs ($H_i$)** | $2 \times 5,000 = 10,000$ | $0 \times 5,000 = 0$ | ₹10,000 |
| **Firing Costs ($F_i$)** | $0 \times 8,000 = 0$ | $4 \times 8,000 = 32,000$ | ₹32,000 |
| **Overtime Costs ($O_i$)** | $0$ | $0$ | ₹0 |
| **Inventory Holding ($I_i$)** | $0$ | $0$ | ₹0 |
| **Total Monthly Cost** | **₹2,50,000** | **₹1,92,000** | **₹4,42,000** |

---

### Optimal LP Output Summary

$$\mathbf{Z^* = \text{₹}4,42,000}$$

$$\begin{aligned} \text{Month 1:} \quad W_1 = 12, \quad H_1 = 2, \quad F_1 = 0, \quad P_1 = 1,200, \quad O_1 = 0, \quad I_1 = 0 \\ \text{Month 2:} \quad W_2 = 8, \quad H_2 = 0, \quad F_2 = 4, \quad P_2 = 800, \quad O_2 = 0, \quad I_2 = 0 \end{aligned}$$






---







# Linear Programming Modeling: Network Bandwidth Allocation

---

### 1. Problem Context & Network Topology

In telecommunication networks, service providers route data traffic between multiple customer sites through interconnecting switches and routers.

```text
                  [a]-------( 6 )-------[b]
                 /   \                 /   \
               (12)   \ (11)     (13) /    (10)
               /       \             /       \
             [A]        +----[c]----+        [B]
                              |
                             (13)
                              |
                             [C]

  - End Users: A, B, C (Connected to edge switches a, b, c)
  - Edge Links: (a-A)=12 Mbps, (b-B)=10 Mbps, (c-C)=13 Mbps
  - Core Inter-switch Links: (a-b)=6 Mbps, (b-c)=13 Mbps, (a-c)=11 Mbps

```

#### Key Requirements

1. **Routing Flexibility:** Traffic between any customer pair (e.g., $A$ and $B$) can be split across a **direct route** (via $a-b$) or an **indirect route** (via $a-c-b$).
2. **Quality of Service (SLA):** Each pair of users must receive at least a minimum baseline bandwidth (e.g., $2\text{ Mbps}$).
3. **Capacity Constraints:** Every physical link has a fixed maximum transmission capacity (bandwidth bottleneck).
4. **Differential Pricing:** The network earns different revenues per Mbps based on customer demand and service level agreements.

---

### 2. General Mathematical Model Formulation

Consider a network with end-user pairs $P$ and physical links $L$.

#### Decision Variables

For each customer pair $p \in P$:

* $x_p$: Traffic routed over the **direct path** (Mbps).
* $y_p$: Traffic routed over the **indirect path** (Mbps).

#### Objective Function

Maximize revenue across all served customer pairs:

$$\text{Maximize } Z = \sum_{p \in P} R_p \cdot (x_p + y_p)$$

Where $R_p$ is the revenue earned per Mbps for customer pair $p$.

#### Constraints

1. **Link Capacity Constraints:** The sum of all direct and indirect traffic paths utilizing a physical link $l \in L$ cannot exceed its capacity $C_l$:

$$\sum_{p \text{ using link } l} x_p + \sum_{p' \text{ using link } l} y_{p'} \le C_l \quad \forall l \in L$$


2. **Minimum SLA Bandwidth Guarantees:**

$$x_p + y_p \ge D_p^{\min} \quad \forall p \in P$$


3. **Non-Negativity Constraints:**

$$x_p, y_p \ge 0 \quad \forall p \in P$$



---

### 3. Step-by-Step Numerical Example

#### Concrete Problem Instance

Consider 3 customer connections:

1. **$A \leftrightarrow B$:** Revenue = **₹300 / Mbps**, SLA Minimum = **2 Mbps**
2. **$B \leftrightarrow C$:** Revenue = **₹200 / Mbps**, SLA Minimum = **2 Mbps**
3. **$A \leftrightarrow C$:** Revenue = **₹400 / Mbps**, SLA Minimum = **2 Mbps**

#### Physical Link Capacity Limits

* Edge links: $C_{aA} = 12\text{ Mbps}$, $C_{bB} = 10\text{ Mbps}$, $C_{cC} = 13\text{ Mbps}$
* Core links: $C_{ab} = 6\text{ Mbps}$, $C_{bc} = 13\text{ Mbps}$, $C_{ac} = 11\text{ Mbps}$

---

#### Decision Variables

* $x_{ab}, y_{ab}$: Direct ($a-b$) and indirect ($a-c-b$) flows for pair $A-B$.
* $x_{bc}, y_{bc}$: Direct ($b-c$) and indirect ($b-a-c$) flows for pair $B-C$.
* $x_{ac}, y_{ac}$: Direct ($a-c$) and indirect ($a-b-c$) flows for pair $A-C$.

---

#### Objective Function Formulation

$$\text{Maximize } Z = 300(x_{ab} + y_{ab}) + 200(x_{bc} + y_{bc}) + 400(x_{ac} + y_{ac})$$

---

#### System Constraints

1. **Edge Link Capacity Limits:**
* Link $a-A$: $x_{ab} + y_{ab} + x_{ac} + y_{ac} \le 12$
* Link $b-B$: $x_{ab} + y_{ab} + x_{bc} + y_{bc} \le 10$
* Link $c-C$: $x_{bc} + y_{bc} + x_{ac} + y_{ac} \le 13$


2. **Core Link Capacity Limits:**
* Inter-switch link $a-b$: $x_{ab} + y_{bc} + y_{ac} \le 6$
* Inter-switch link $b-c$: $y_{ab} + x_{bc} + y_{ac} \le 13$
* Inter-switch link $a-c$: $y_{ab} + y_{bc} + x_{ac} \le 11$


3. **SLA Minimum Demands:**
* $x_{ab} + y_{ab} \ge 2$
* $x_{bc} + y_{bc} \ge 2$
* $x_{ac} + y_{ac} \ge 2$


4. **Non-Negativity:** $x_{ab}, y_{ab}, x_{bc}, y_{bc}, x_{ac}, y_{ac} \ge 0$

---

### 4. Numerical Optimization Output & Allocation Analysis

Solving this LP yields the following optimal bandwidth allocation:

| Customer Pair | Direct Flow $x_p$ (Mbps) | Indirect Flow $y_p$ (Mbps) | Total Allocated Bandwidth (Mbps) | Revenue Realized (₹) |
| --- | --- | --- | --- | --- |
| **$A \leftrightarrow B$** | $x_{ab} = 0.0$ | $y_{ab} = 7.0$ | **7.0 Mbps** | $7.0 \times 300 = \text{₹}2,100$ |
| **$B \leftrightarrow C$** | $x_{bc} = 1.5$ | $y_{bc} = 1.5$ | **3.0 Mbps** | $3.0 \times 200 = \text{₹}600$ |
| **$A \leftrightarrow C$** | $x_{ac} = 5.0$ | $y_{ac} = 0.0$ | **5.0 Mbps** | $5.0 \times 400 = \text{₹}2,000$ |
| **Total** |  |  |  | **$Z^* = \text{₹}4,700$** |

#### Link Utilization & Bottleneck Check

* **Edge Link $b-B$ Utilization:** $y_{ab} + x_{bc} + y_{bc} = 7.0 + 1.5 + 1.5 = 10.0\text{ Mbps}$ (**100% Saturated Bottleneck**).
* **Core Link $a-b$ Utilization:** $x_{ab} + y_{bc} + y_{ac} = 0.0 + 1.5 + 0.0 = 1.5\text{ Mbps}$ (Underutilized).
* **High Margin Priority:** $A \leftrightarrow C$ (₹400/Mbps) gets 5 Mbps (limited by edge link $a-A$), while $A \leftrightarrow B$ leverages the higher capacity indirect path $a-c-b$ to maximize revenue.

---

### 5. Architectural Trade-offs & Modeling Scalability

| Formulation Approach | Variable Scaling | Constraint Scaling | Structural Problem |
| --- | --- | --- | --- |
| **Path-Based Formulation** (Used above) | $\mathcal{O}(\text{Number of Paths})$ | $\mathcal{O}(\text{Number of Links})$ | Exponential growth of variables in large networks with many alternate paths. |
| **Flow-Based Formulation** (Edge-Node Flow) | $\mathcal{O}(\Vert{}V\Vert{} \cdot \Vert{}E\Vert{})$ | $\mathcal{O}(\Vert{}V\Vert{} \cdot \Vert{}K\Vert{})$ | Keeps LP size polynomial using standard multi-commodity flow conservation constraints at each node. |



---



# Network Flows & Maximum Flow Formulation

---

### 1. Problem Definition & Network Flow Principles

A **Flow Network** is a directed graph $G = (V, E)$ where:

* **Source ($s$):** Node with only outgoing edges (where the commodity originates).
* **Sink ($t$):** Node with only incoming edges (where the commodity terminates).
* **Edge Capacities ($c(u, v)$):** Each edge $(u, v) \in E$ has a non-negative maximum capacity limit $c(u, v) \ge 0$.

```text
                  ( Capacity = 10 )
                      +-------+
                     /         \
       [ Source s ] +-----------> [ Node v ] -----------> [ Sink t ]
                     \         /
                      +-------+
                  ( Capacity = 5 )

```

#### Core Physical Conditions

1. **Capacity Constraint:** The actual flow $f(u, v)$ across any edge cannot exceed its rated capacity:

$$0 \le f(u, v) \le c(u, v) \quad \forall (u, v) \in E$$


2. **Flow Conservation:** At every intermediate node $u \in V \setminus \{s, t\}$, the total incoming flow must equal the total outgoing flow (no accumulation or leakage):

$$\sum_{w \in V} f(w, u) = \sum_{v \in V} f(u, v) \quad \forall u \in V \setminus \{s, t\}$$


3. **Total Flow Value ($\vert{}f\vert{}$):** The total net commodity leaving the source:

$$\vert{}f\vert{} = \sum_{v \in V} f(s, v)$$



---

### 2. Linear Programming Formulation of Max-Flow

Unlike path-based formulations, the edge-based flow formulation scales polynomially in terms of variables and constraints.

#### Decision Variables

Assign one continuous variable $f_{u,v}$ for each edge $(u, v) \in E$.

#### Mathematical LP Model

$$\text{Maximize } \vert{}f\vert{} = \sum_{v:(s, v) \in E} f_{s, v}$$

$$\begin{aligned} \text{Subject to constraints: } & f_{u, v} \le c(u, v) \quad \forall (u, v) \in E \quad \text{(Capacity Limit)} \\ & \sum_{w:(w, u) \in E} f_{w, u} - \sum_{v:(u, v) \in E} f_{u, v} = 0 \quad \forall u \in V \setminus \{s, t\} \quad \text{(Conservation)} \\ & f_{u, v} \ge 0 \quad \forall (u, v) \in E \quad \text{(Non-Negativity)} \end{aligned}$$

---

### 3. Step-by-Step Numerical Example

#### Concrete Problem Network

Consider the following 4-node flow network:

* **Edges & Capacities:**
* $(s, A) = 10$, $(s, B) = 8$
* $(A, B) = 5$, $(A, t) = 7$
* $(B, t) = 10$



```text
                       (10)
                 +--------------> [ A ] --------------+ (7)
                /                  |                   \
     [ s ]-----+                   | (5)                +-----> [ t ]
                \                  v                   /
                 +--------------> [ B ] --------------+ (10)
                        (8)

```

#### Linear Program Formulation

$$\text{Maximize } Z = f_{s, A} + f_{s, B}$$

$$\begin{aligned} \text{Constraints: } & f_{s, A} \le 10, \quad f_{s, B} \le 8, \quad f_{A, B} \le 5, \quad f_{A, t} \le 7, \quad f_{B, t} \le 10 \\ & f_{s, A} - f_{A, B} - f_{A, t} = 0 \quad \text{(Node A Conservation)} \\ & f_{s, B} + f_{A, B} - f_{B, t} = 0 \quad \text{(Node B Conservation)} \\ & \text{All } f_{u, v} \ge 0 \end{aligned}$$

#### Step-by-Step Numerical Flow Push

1. **Push flow along path $s \to A \to t$:**
* Bottleneck capacity = $\min(10, 7) = 7$.
* Assign $f_{s, A} = 7$, $f_{A, t} = 7$.


2. **Push flow along path $s \to A \to B \to t$:**
* Remaining capacity on $(s, A) = 10 - 7 = 3$.
* Bottleneck capacity = $\min(3, 5, 10) = 3$.
* Increase $f_{s, A} \gets 7 + 3 = 10$.
* Assign $f_{A, B} = 3$, $f_{B, t} = 3$.


3. **Push flow along path $s \to B \to t$:**
* Remaining capacity on $(B, t) = 10 - 3 = 7$.
* Bottleneck capacity = $\min(8, 7) = 7$.
* Assign $f_{s, B} = 7$, $f_{B, t} \gets 3 + 7 = 10$.



#### Optimal Numerical Output

| Edge $(u,v)$ | Flow Assigned $f_{u,v}$ | Capacity $c(u,v)$ | Saturation Status |
| --- | --- | --- | --- |
| $(s, A)$ | **10** | 10 | **Saturated** |
| $(s, B)$ | **7** | 8 | Un-saturated |
| $(A, B)$ | **3** | 5 | Un-saturated |
| $(A, t)$ | **7** | 7 | **Saturated** |
| $(B, t)$ | **10** | 10 | **Saturated** |

$$\text{Maximum Total Flow } \vert{}f^*\vert{} = f_{s, A} + f_{s, B} = 10 + 7 = \mathbf{17}$$

---

### 4. Residual Graphs & The Ford-Fulkerson Method

The **Ford-Fulkerson algorithm** iteratively finds augmenting paths in a **Residual Graph $G_f$** to increment total flow.

#### Residual Edges Definition

For every edge $(u, v)$ carrying flow $f(u, v)$:

* **Forward Residual Edge:** $c_f(u, v) = c(u, v) - f(u, v)$ (Remaining available capacity).
* **Backward Residual Edge:** $c_f(v, u) = f(u, v)$ (Allows cancelling/diverting previously committed flow).

```text
  Original Edge (Flow / Capacity):   Residual Graph Representation:
            ( 3 / 10 )                        Forward Capacity = 7
      [ u ] ----------> [ v ]           [ u ] -------------------> [ v ]
                                              <-------------------
                                               Backward Capacity = 3

```

---

### 5. Max-Flow Min-Cut Theorem

An $s-t$ **Cut $(S, T)$** is a partition of vertices $V$ into two disjoint sets $S$ and $T$ such that $s \in S$ and $t \in T$. The **capacity of the cut** $C(S, T)$ is the sum of capacities of edges pointing from $S$ to $T$:

$$C(S, T) = \sum_{u \in S, v \in T, (u, v) \in E} c(u, v)$$

#### Max-Flow Min-Cut Theorem Statement

For any network, the following three statements are equivalent:

1. $f$ is a maximum flow in $G$.
2. The residual network $G_f$ contains no augmenting paths from $s$ to $t$.
3. Total Maximum Flow Value = Capacity of the Minimum Cut:

$$\max \vert{}f\vert{} = \min_{(S, T)} C(S, T)$$

#### Numerical Cut Verification from Example Above

Let $S = \{s, A\}$ and $T = \{B, t\}$.

* Cut edges extending from $S$ to $T$: $(A, t)$ and $(s, B)$.
* Cut Capacity $C(S, T) = c(A, t) + c(s, B) = 7 + 10 = \mathbf{17}$.
* Since Maximum Flow ($17$) = Minimum Cut Capacity ($17$), optimality is mathematically guaranteed.

---

### 6. Algorithmic Complexity Comparison

| Algorithm | Path Selection Strategy | Time Complexity | Notes / Bottleneck |
| --- | --- | --- | --- |
| **Generic Ford-Fulkerson** | Arbitrary Depth-First Search (DFS) | $\mathcal{O}(\Vert{}E\Vert{} \cdot \Vert{}f^*\Vert{})$ | Pseudo-polynomial; depends on numeric edge capacity values $C$. |
| **Edmonds-Karp Algorithm** | Shortest Augmenting Path via Breadth-First Search (BFS) | $\mathcal{O}(\Vert{}V\Vert{} \cdot \Vert{}E\Vert{}^2)$ | Strongly polynomial; independent of capacity magnitude. |
| **Dinic's Algorithm** | Layered networks + Blocking flows | $\mathcal{O}(\Vert{}V\Vert{}^2 \cdot \Vert{}E\Vert{})$ | Highly efficient for dense networks and unit-capacity graphs ($\mathcal{O}(\sqrt{\Vert{}V\Vert{}} \cdot \Vert{}E\Vert{})$). |




---





# Problem Reductions & Bipartite Matching

---

### 1. Conceptual Framework of Reductions

A **reduction** is an algorithmic transformation that translates an instance of Problem $A$ into an equivalent instance of Problem $B$. If an efficient solver exists for Problem $B$, it can be leveraged to solve Problem $A$ without designing a custom algorithm from scratch.

```text
                       +-------------------------------------+
                       |         REDUCTION WORKFLOW          |
                       +-------------------------------------+
                                          |
  [ Input Instance for Problem A ]       |
                 |                        |
                 v                        |
       ( Pre-processing Step )           |
                 |                        |  Overall Execution Time
                 v                        |  = Pre-processing Time
  [ Transformed Input for Problem B ]     |  + Time to Solve B
                 |                        |  + Post-processing Time
                 v                        |
     ( Black-Box Solver for B )           |
                 |                        |
                 v                        |
  [ Solution Output for Problem B ]       |
                 |                        |
                 v                        |
       ( Post-processing Step )          |
                 |                        |
                 v                        |
  [ Solution Output for Problem A ]       |
                                          |
                       +-------------------------------------+

```

#### Dual Significance of Reductions

1. **Positive Reduction (Algorithm Design):** If Problem $A$ reduces efficiently to Problem $B$, and an efficient solver exists for $B$, then $A$ can be solved efficiently.
2. **Negative Reduction (Hardness Proofs):** If Problem $A$ is known to be computationally hard, and $A$ reduces to $B$, then $B$ must also be at least as hard as $A$.

---

### 2. Maximum Bipartite Matching Problem

A **Bipartite Graph** $G = (V_0 \cup V_1, E)$ is a graph whose vertices can be partitioned into two disjoint sets $V_0$ and $V_1$ such that every edge $(u, v) \in E$ connects a vertex in $V_0$ to a vertex in $V_1$. No edges exist within $V_0$ or within $V_1$.

#### Problem Statement

A **matching** $M \subseteq E$ is a subset of edges such that no two edges share a common vertex.

* **Maximum Matching:** Find a matching $M$ that maximizes the number of selected edges ($\vert{}M\vert{}$).
* **Perfect Matching:** A matching where every vertex in $V_0 \cup V_1$ is incident to an edge in $M$.

---

### 3. Reduction: Bipartite Matching to Maximum Network Flow

To solve Maximum Bipartite Matching using a Max-Flow solver, transform the undirected bipartite graph into a directed flow network $G' = (V', E')$.

#### Reduction Construction Rules

1. **Add Special Nodes:** Create a source node $s$ and a sink node $t$.
2. **Direct Left-to-Right Edges:** Direct all original edges from $V_0$ to $V_1$ with capacity $c(u, v) = 1$.
3. **Connect Source to Left Set:** Add a directed edge $(s, u)$ for every $u \in V_0$ with capacity $c(s, u) = 1$.
4. **Connect Right Set to Sink:** Add a directed edge $(v, t)$ for every $v \in V_1$ with capacity $c(v, t) = 1$.

```text
               Original Bipartite Graph          Flow Network Transformation
                 
                   (V0)     (V1)                  (s) ---> (V0) ---> (V1) ---> (t)
                  [ A ] --- [ M ]               [ s ] -(1)-> [ A ] -(1)-> [ M ] -(1)-> [ t ]
                  [ B ] --- [ H ]               [ s ] -(1)-> [ B ] -(1)-> [ H ] -(1)-> [ t ]
                  [ C ] --- [ E ]               [ s ] -(1)-> [ C ] -(1)-> [ E ] -(1)-> [ t ]

```

#### Correctness of the Reduction

* **Unit Capacity Constraint:** Setting all capacities to $1$ ensures that:
* At most 1 unit of flow leaves $s$ to each vertex $u \in V_0$ (each $u$ is matched at most once).
* At most 1 unit of flow leaves each vertex $v \in V_1$ to $t$ (each $v$ is matched at most once).


* **Integrity Theorem:** Since all capacities are integers ($1$), standard Max-Flow algorithms (like Ford-Fulkerson or Dinic's) produce integer-valued flows $f(u, v) \in \{0, 1\}$.
* **Solution Mapping:** An edge $(u, v)$ is included in the matching $M$ if and only if $f(u, v) = 1$. The maximum flow value $\vert{}f^*\vert{}$ equals the maximum matching size $\vert{}M^*\vert{}$.

---

### 4. Step-by-Step Numerical Example

#### Concrete Problem Instance

Assign **4 Instructors ($V_0$)** to **4 Courses ($V_1$)** based on preferences:

* **Instructors ($V_0$):** $A$ (Abbas), $C$ (Chitra), $M$ (Madan), $S$ (Sunita)
* **Courses ($V_1$):** $M_{ath}$, $H_{ist}$, $B_{io}$, $E_{con}$
* **Allowed Edge Preferences ($E$):**
* $A \to \{H_{ist}, B_{io}\}$
* $C \to \{M_{ath}, B_{io}\}$
* $M \to \{H_{ist}, E_{con}\}$
* $S \to \{E_{con}\}$



---

#### Flow Network Construction

Construct flow network $G'$:

* **Source Edges:** $(s, A), (s, C), (s, M), (s, S)$ — each with capacity $1$.
* **Preference Edges:** $(A, H_{ist}), (A, B_{io}), (C, M_{ath}), (C, B_{io}), (M, H_{ist}), (M, E_{con}), (S, E_{con})$ — each with capacity $1$.
* **Sink Edges:** $(M_{ath}, t), (H_{ist}, t), (B_{io}, t), (E_{con}, t)$ — each with capacity $1$.

---

#### Execution of Ford-Fulkerson / Augmenting Paths

1. **Path 1:** $s \to C \to M_{ath} \to t$
* Push flow = 1.
* Matches: **Chitra $\to$ Math**.


2. **Path 2:** $s \to A \to B_{io} \to t$
* Push flow = 1.
* Matches: **Abbas $\to$ Biology**.


3. **Path 3:** $s \to M \to H_{ist} \to t$
* Push flow = 1.
* Matches: **Madan $\to$ History**.


4. **Path 4:** $s \to S \to E_{con} \to t$
* Push flow = 1.
* Matches: **Sunita $\to$ Economics**.



---

#### Optimal Numerical Output

| Instructor ($V_0$) | Assigned Course ($V_1$) | Flow Value $f(u,v)$ |
| --- | --- | --- |
| **Abbas ($A$)** | Biology ($B_{io}$) | **1** |
| **Chitra ($C$)** | Math ($M_{ath}$) | **1** |
| **Madan ($M$)** | History ($H_{ist}$) | **1** |
| **Sunita ($S$)** | Economics ($E_{con}$) | **1** |

$$\text{Maximum Flow } \vert{}f^*\vert{} = 4 \implies \text{Maximum Matching Size } \vert{}M^*\vert{} = 4 \quad \text{(Perfect Matching)}$$

---

### 5. Algorithmic Complexity

By reducing Bipartite Matching to Max-Flow, any Max-Flow solver can be applied:

| Solver Algorithm | Reduction Flow Bound | Total Bipartite Matching Complexity |
| --- | --- | --- |
| **Ford-Fulkerson** | $\mathcal{O}(\vert{}E\vert{} \cdot \vert{}f^*\vert{})$ | **$\mathcal{O}(\vert{}V\vert{} \cdot \vert{}E\vert{})$** (since $\vert{}f^*\vert{} \le \vert{}V\vert{}$) |
| **Dinic's Algorithm** | $\mathcal{O}(\sqrt{\vert{}V\vert{}} \cdot \vert{}E\vert{})$ on unit networks | **$\mathcal{O}(\sqrt{\vert{}V\vert{}} \cdot \vert{}E\vert{})$** (Fastest general bound) |
| **Hopcroft-Karp Algorithm** | Specialized matching solver | **$\mathcal{O}(\sqrt{\vert{}V\vert{}} \cdot \vert{}E\vert{})$** |

#### Complexity Evaluation

* **Pre-processing Time:** $\mathcal{O}(\vert{}V\vert{} + \vert{}E\vert{})$ to build $G'$.
* **Post-processing Time:** $\mathcal{O}(\vert{}E\vert{})$ to filter edges where $f(u, v) = 1$.
* **Overall Time Complexity:** **$\mathcal{O}(\sqrt{\vert{}V\vert{}} \cdot \vert{}E\vert{})$** using Dinic's or Hopcroft-Karp.




---





# Intractability, Verification, and Checking Algorithms

---

### 1. Conceptual Search Space & Algorithmic Efficiency

In many computational problems, brute-force solutions evaluate all candidates in an exponential search space.

```text
  Brute-Force Search Space (Exponential Size: O(2^n) or O(n!))
  +-------------------------------------------------------------+
  |  [ Candidate 1 ]   [ Candidate 2 ]   ...   [ Candidate K ]  |
  +-------------------------------------------------------------+
                               |
                               | Polynomial Shortcut Algorithm
                               v
               +-------------------------------+
               | Optimal/Feasible Solution     |
               +-------------------------------+

```

* **Polynomial Shortcuts:** Algorithms like Dijkstra's (Shortest Path) or Kruskal's (Minimum Spanning Tree) prune the exponential space, producing an optimal solution in polynomial time.
* **Intractable Problems:** For many practical problems, no polynomial-time algorithm is known. Searching for a solution seems to require examining an exponential number of candidates.

---

### 2. Generative vs. Checking (Verification) Algorithms

Distinguishing between **generating** a solution and **checking** (verifying) a candidate solution is central to computational complexity theory.

```text
                       GENERATING ALGORITHM
  [ Input Instance I ] --------------------------> [ Solution S ]
                             (Often Hard)

                      VERIFICATION ALGORITHM
  [ Input I ] + [ Certificate S ] ---------------> [ YES / NO ]
                             (Fast Check)

```

#### Factorization Analogy

* **Generating Problem:** Given a large integer $N$, find two prime factors $P$ and $Q$ such that $P \cdot Q = N$.
* **Checking Problem:** Given $N$ and candidate factors $(P, Q)$, verify whether $P \cdot Q = N$.
* **Key Insight:** Generating factors is computationally difficult, but checking candidate factors requires a single multiplication step ($\mathcal{O}(\log^2 N)$ time).

---

### 3. Decision Problems and Decision-Optimization Equivalences

To analyze verification, optimization problems are transformed into **Decision Problems** by introducing a numeric target bound $K$.

#### Optimization vs. Decision Formulations

| Problem | Optimization Version (Find Best) | Decision / Checking Version (Bound $K$) |
| --- | --- | --- |
| **Traveling Salesman (TSP)** | Find a Hamiltonian cycle of **minimum** total weight. | Does there exist a Hamiltonian cycle of total weight **$\le K$**? |
| **Independent Set** | Find an independent set of **maximum** cardinality. | Does there exist an independent set of size **$\ge K$**? |
| **Vertex Cover** | Find a vertex cover of **minimum** cardinality. | Does there exist a vertex cover of size **$\le K$**? |

#### Binary Search Reduction

Solving a decision version enables solving the optimization version using binary search over the bound $K$.

```text
                             Binary Search over Bound K
  Range: [0, Sum of Edge Weights]
  
            Test K = Mid
           /            \
     Returns YES     Returns NO
         /                \
  Search [0, Mid]    Search [Mid+1, High]

```

---

### 4. Canonical Problems & Polynomial Verification

#### Boolean Satisfiability (3-SAT)

Given a Boolean formula in Conjunctive Normal Form (CNF)—a conjunction of clauses where each clause is a disjunction of literals:

$$\phi = (x_1 \lor \neg x_2 \lor x_3) \land (\neg x_1 \lor x_2 \lor x_4) \land (\neg x_3 \lor \neg x_4 \lor x_2)$$

* **Decision Problem:** Does there exist an assignment of truth values to variables that evaluates $\phi$ to `TRUE`?
* **Verification:** Given a truth assignment (e.g., $x_1=\text{T}, x_2=\text{F}, x_3=\text{T}, x_4=\text{F}$), evaluate each clause in linear time $\mathcal{O}(\vert{}\phi\vert{})$.

#### Independent Set

An **Independent Set** $S \subseteq V$ in a graph $G=(V, E)$ is a subset of vertices such that no two vertices in $S$ are adjacent:

$$\forall u, v \in S, \quad (u, v) \notin E$$

* **Verification:** Given candidate subset $S$, verify that $\vert{}S\vert{} \ge K$ and iterate over all pairs in $S$ to ensure no edge exists ($\mathcal{O}(\vert{}V\vert{} + \vert{}E\vert{})$ time).

#### Vertex Cover

A **Vertex Cover** $C \subseteq V$ in a graph $G=(V, E)$ is a subset of vertices such that every edge in $E$ has at least one endpoint in $C$:

$$\forall (u, v) \in E, \quad u \in C \lor v \in C$$

* **Verification:** Given candidate subset $C$, verify that $\vert{}C\vert{} \le K$ and check that every edge $(u, v) \in E$ touches $C$ ($\mathcal{O}(\vert{}E\vert{})$ time).

---

### 5. Duality Proof: Independent Set $\iff$ Vertex Cover

#### Theorem Statement

In any graph $G = (V, E)$, a subset $S \subseteq V$ is an **Independent Set** if and only if its complement $V \setminus S$ is a **Vertex Cover**.

```text
    Graph Vertices V = S U (V \ S)
  +-------------------------------------------------+
  |  Independent Set S   |   Vertex Cover V \ S     |
  |                      |                          |
  |  (No internal edges) |  (Touches all edges in E)|
  +-------------------------------------------------+

```

#### Numerical Example

Consider a 5-vertex graph $G = (V, E)$ with $V = \{1, 2, 3, 4, 5\}$ and edges $E = \{(1,2), (1,3), (2,3), (3,4), (4,5)\}$.

```text
                   (1) ------- (2)
                     \         /
                      \       /
                         (3)
                          |
                         (4)
                          |
                         (5)

```

1. **Test Independent Set $S = \{1, 4\}$:**
* Pair $(1, 4) \notin E \implies S$ is a valid Independent Set of size $K = 2$.


2. **Complement $V \setminus S = \{2, 3, 5\}$:**
* Size $= \vert{}V\vert{} - K = 5 - 2 = 3$.
* Check all edges against $V \setminus S$:
* $(1,2) \implies 2 \in \{2, 3, 5\}$
* $(1,3) \implies 3 \in \{2, 3, 5\}$
* $(2,3) \implies 2, 3 \in \{2, 3, 5\}$
* $(3,4) \implies 3 \in \{2, 3, 5\}$
* $(4,5) \implies 5 \in \{2, 3, 5\}$


* Every edge has at least one endpoint in $V \setminus S \implies \{2, 3, 5\}$ is a valid Vertex Cover.



#### Algebraic Correctness

* $(\implies)$ Assume $S$ is an Independent Set. Suppose $V \setminus S$ is not a Vertex Cover. Then there exists an edge $(u, v) \in E$ such that $u \notin V \setminus S$ and $v \notin V \setminus S$. This implies $u \in S$ and $v \in S$, contradicting the assumption that $S$ is an Independent Set.
* $(\impliedby)$ Assume $V \setminus S$ is a Vertex Cover. Suppose $S$ is not an Independent Set. Then there exists an edge $(u, v) \in E$ with $u, v \in S$. Thus $u \notin V \setminus S$ and $v \notin V \setminus S$, contradicting that $V \setminus S$ covers all edges.

---

### 6. Algorithmic Complexity Bounds

| Problem | Search Space Size | Verification Complexity | Best Known Solver Complexity |
| --- | --- | --- | --- |
| **Boolean SAT** | $\mathcal{O}(2^n)$ truth valuations | $\mathcal{O}(n + m)$ | Exponential: $\mathcal{O}(2^n)$ |
| **TSP (Decision)** | $\mathcal{O}(n!)$ permutations | $\mathcal{O}(n)$ | Exponential: $\mathcal{O}(n^2 2^n)$ (Dynamic Programming) |
| **Independent Set** | $\mathcal{O}\left(\binom{n}{k}\right)$ subsets | $\mathcal{O}(n + m)$ | Exponential: $\mathcal{O}(2^n)$ |
| **Vertex Cover** | $\mathcal{O}\left(\binom{n}{k}\right)$ subsets | $\mathcal{O}(m)$ | Parameterized: $\mathcal{O}(1.2738^k + k \cdot n)$ |




---







# Computational Complexity: P, NP, and NP-Completeness

---

### 1. The Complexity Classes P and NP

In computational complexity theory, decision problems are classified based on the resources required to solve or verify them.

#### Class P (Polynomial Time)

**P** is the set of all decision problems that can be **solved** by a deterministic algorithm in polynomial time relative to the size of the input $n$:


$$T(n) = \mathcal{O}(n^k) \quad \text{for some constant } k \ge 1$$

* **Examples:** Shortest Path (Dijkstra's), Minimum Spanning Tree (Kruskal's), Linear Programming, Maximum Network Flow.

#### Class NP (Nondeterministic Polynomial Time)

**NP** is the set of all decision problems for which a proposed solution (certificate or witness) can be **verified** by a deterministic algorithm in polynomial time:


$$T_{\text{verify}}(n) = \mathcal{O}(n^k)$$

```text
                  +-------------------------------------------------+
                  |                   CLASS NP                      |
                  |  (Problems verifiably solvable in Poly-Time)   |
                  |                                                 |
                  |   +-----------------------------------------+   |
                  |   |                CLASS P                  |   |
                  |   |  (Problems SOLVABLE in Poly-Time)       |   |
                  |   |                                         |   |
                  |   |   Examples: Shortest Path, MST, Max Flow|   |
                  |   +-----------------------------------------+   |
                  |                                                 |
                  |   Non-P examples in NP (under P != NP):         |
                  |   3-SAT, TSP, Vertex Cover, Independent Set     |
                  +-------------------------------------------------+

```

#### Fundamental Inclusion Relationship

$$\mathbf{P \subseteq NP}$$

* **Proof:** If a problem is in $\mathbf{P}$, a deterministic algorithm can generate the optimal solution from scratch in polynomial time. Therefore, checking a candidate solution simply involves generating the solution in polynomial time and comparing it against the candidate.

---

### 2. Polynomial-Time Reductions & Chain of Reductions

A **Polynomial-Time Many-One Reduction** ($A \le_p B$) converts an instance $I_A$ of problem $A$ into an instance $I_B$ of problem $B$ in polynomial time $\mathcal{O}(\text{poly}(n))$ such that $I_A$ has a "YES" answer if and only if $I_B$ has a "YES" answer.

#### Chain of Polynomial Reductions

By composing polynomial-time transformations, hardness propagates across disparate domains:

$$\text{SAT} \le_p \text{3-SAT} \le_p \text{Independent Set} \le_p \text{Vertex Cover}$$

```text
  [ SAT ] ----> [ 3-SAT ] ----> [ Independent Set ] ----> [ Vertex Cover ]
            (Clause        (Gadget Construction:          (Set Complement
            Splitting)      Triangles & Green Edges)       Duality: S vs V\S)

```

---

### 3. Concrete Reduction: 3-SAT $\le_p$ Independent Set

#### Reduction Rules

Given a 3-SAT formula $\phi$ with $m$ clauses:

1. **Clause Triangles:** For each clause $C_i = (l_1 \lor l_2 \lor l_3)$, construct a triangle of 3 vertices, where each vertex represents a literal $l_j$.
2. **Conflict Edges:** Add an edge between any pair of vertices in different triangles that represent contradictory literals (e.g., between $x$ and $\neg x$).
3. **Target Bound:** Set the target Independent Set capacity limit to **$K = m$** (where $m$ is the total number of clauses).

---

#### Step-by-Step Numerical Example

Consider the 3-SAT formula with $m = 3$ clauses:


$$\phi = (x \lor y \lor z) \land (\neg x \lor \neg y \lor z) \land (\neg z \lor x \lor y)$$

* **Step 1: Construct Clause Triangles**
* Clause $C_1$: Triangle with vertices $\{x_1, y_1, z_1\}$
* Clause $C_2$: Triangle with vertices $\{\neg x_2, \neg y_2, z_2\}$
* Clause $C_3$: Triangle with vertices $\{\neg z_3, x_3, y_3\}$
* Total vertices $\vert{}V\vert{} = 3 \times 3 = 9$.


* **Step 2: Connect Contradictory Literals (Conflict Edges)**
* Connect $x_1$ to $\neg x_2$.
* Connect $y_1$ to $\neg y_2$.
* Connect $z_1$ and $z_2$ to $\neg z_3$.


* **Step 3: Evaluate Independent Set of Target Size $K = 3$**
* Since each clause triangle is fully connected, an independent set can pick **at most 1 vertex per triangle**.
* To reach $K = 3$, exactly **1 vertex must be selected from each triangle** without picking conflicting literals.



```text
  Triangle C1: ( x1 ) ---- ( y1 ) ---- ( z1 )
                  \          /
                   \        /
                    ( \neg x2 )  <-- Conflict edge connects x1 and \neg x2

```

* **Valid Selection:** Pick $\{y_1, z_2, x_3\}$:
* $y_1 \implies y = \text{TRUE}$
* $z_2 \implies z = \text{TRUE}$
* $x_3 \implies x = \text{TRUE}$


* **Result:** No conflict edges connect $y_1, z_2, x_3$. The set $\{y_1, z_2, x_3\}$ is an independent set of size $K = 3$, corresponding directly to a satisfying truth assignment $(x=\text{T}, y=\text{T}, z=\text{T})$.

---

### 4. NP-Completeness & Cook-Levin Theorem

#### Formal Definitions

* **NP-Hard:** A problem $B$ is **NP-Hard** if every problem $A \in \mathbf{NP}$ can be reduced to $B$ in polynomial time ($A \le_p B \quad \forall A \in \mathbf{NP}$). NP-Hard problems do not need to be in $\mathbf{NP}$ themselves.
* **NP-Complete:** A problem $B$ is **NP-Complete** if:
1. $B \in \mathbf{NP}$ (it has a polynomial-time verification algorithm).
2. $B$ is **NP-Hard** ($A \le_p B \quad \forall A \in \mathbf{NP}$).



```text
                  +-------------------------------------------------+
                  |                   CLASS NP                      |
                  |                                                 |
                  |   +-------------------+    +----------------+   |
                  |   |      CLASS P      |    |  NP-COMPLETE   |   |
                  |   |                   |    |                |   |
                  |   |  (Poly-Solvable)  |    |  (Hardest in)  |   |
                  |   +-------------------+    +----------------+   |
                  +------------------------------------|------------+
                                                       |
                                                       v
                                               +----------------+
                                               |    NP-HARD     |
                                               |  (At least as  |
                                               |  hard as NP)   |
                                               +----------------+

```

#### The Cook-Levin Theorem (1971)

> **Theorem:** **Boolean Satisfiability (SAT) is NP-Complete**.

* **Significance:** Cook and Levin independently proved that the nondeterministic computation of *any* arbitrary polynomial-time verifier machine can be encoded as a Boolean formula in Conjunctive Normal Form (CNF). Thus, SAT served as the foundational anchor for proving the NP-Completeness of thousands of downstream problems via reductions.

---

### 5. The $P \text{ vs } NP$ Millennium Prize Problem

The central unresolved question in theoretical computer science asks:

$$\mathbf{Is \quad P = NP \quad \text{or} \quad P \neq NP?}$$

```text
       If P = NP (Idealized World):             If P != NP (Widely Believed):

           +-----------------+                      +-----------------+
           |     P = NP      |                      |      NP         |
           |                 |                      |  +-----------+  |
           |  (Verification  |                      |  |     P     |  |
           |   == Generation)|                      |  +-----------+  |
           +-----------------+                      +-----------------+

```

#### Implications of Potential Proofs

| Scenario | Practical Implications | Theoretical Impact |
| --- | --- | --- |
| **If $\mathbf{P = NP}$** | Every problem with a fast verification algorithm can be solved fast. Modern cryptography (RSA, ECC) breaks instantly; combinatorial optimization (TSP, Scheduling, Protein Folding) becomes tractable. | $\mathbf{P = NP = NP\text{-Complete}}$ |
| **If $\mathbf{P \neq NP}$** (Conjectured) | Verification is strictly easier than generation. Polynomial-time algorithms can never exist for NP-Complete problems, confirming that approximate or heuristic algorithms are necessary. | $\mathbf{P \neq NP}$; NP-Complete problems remain intractable in the worst case. |

* **Millennium Prize:** The Clay Mathematics Institute listed $P \text{ vs } NP$ as one of the seven Millennium Prize Problems in 2000, offering a **$1,000,000 prize** for the first correct mathematical proof.

---

### Summary Table of Complexity Classes

| Complexity Class | Poly-Time Solvable?                         | Poly-Time Verifiable? | Representative Examples                                                  |
| ---------------- | ------------------------------------------- | --------------------- | ------------------------------------------------------------------------ |
| **P**            | **YES**                                     | **YES**               | Dijkstra's Shortest Path, MST, Linear Programming, BFS/DFS      |
| **NP**           | Unknown (No for NP-Complete if $P \neq NP$) | **YES**               | 3-SAT, Traveling Salesman (Decision), Vertex Cover, Subset Sum  |
| **NP-Complete**  | **NO** (Assuming $P \neq NP$)               | **YES**               | 3-SAT, Circuit-SAT, Maximum Clique, Graph Coloring              |
| **NP-Hard**      | **NO** (Assuming $P \neq NP$)               | Not necessarily       | Halting Problem, TSP (Optimization), Integer Linear Programming |

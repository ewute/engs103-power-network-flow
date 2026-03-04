# Optimal Power Flow and Economic Dispatch: A Network Flow Approach

## Objective
Our goal is to minimize the total operational and transmission costs of distributing electrical power from generation facilities to population centers across a regional grid, subject to physical generation capacities and transmission line thermal limits.

## Introduction
The management of electrical power grids requires a delicate orchestration of generation and transmission. Grid operators—such as Independent System Operators (ISOs)—must continuously match the electricity supply to consumer demand while minimizing costs and preventing system failures. 

This project explores a simplified model of the New Hampshire electric grid using Network Flow. We formulate the problem as a Minimum Cost Network Flow model on a bipartite graph. Through iterative augmentations of our model, from a pure geographic optimal power flow penalty to a fully constrained economic dispatch model with transmission line bottlenecks, we observe how emergent mathematical behaviors accurately mirror complex real-world energy economics.

## Background
The electrical grid is divided into nodes (generation plants and municipalities) and edges (transmission lines). Pushing power across these connections entails physical losses and infrastructure maintenance costs, while power generation itself varies drastically in cost depending on the fuel type.

For this study, we utilize real geographic data for 10 major towns in New Hampshire and 5 key regional generation facilities (varying from Nuclear and Hydro baseload to Natural Gas mid-load and Coal peakers). Demands and capacities were scaled relative to typical multi-plant operational envelopes, challenging our solver to distribute approximately 2,650 MW of load across an available 2,800 MW of generation capacity.

## Literature Review
The core structure of our model is built upon two famous Operations Research formalisms in power systems engineering:
1. Optimal Power Flow (OPF): As explored by Bialek (1996), pushing electricity through high-voltage alternating current (HVAC) lines incurs physical $I^2R$ ohmic heat losses. In energy markets, these penalties, along with infrastructural up-keep, are codified as "wheeling tariffs" and congestion charges. We employ a proxy transmission tariff to emulate geographical friction.
2. Economic Dispatch: Wood and Wollenberg (2012) define economic dispatch as the short-term determination of optimal generator output to meet load at the lowest cost. Short-run marginal costs vary heavily by fuel type. According to the U.S. Energy Information Administration's Levelized Cost of Energy (LCOE) frameworks, grid controllers prioritize high-capital but low-marginal-cost "baseload" facilities (e.g., Hydro and Nuclear) before dispatching expensive fossil-fuel peakers (e.g., Natural Gas and Coal). 
Furthermore, the U.S. Department of Energy’s National Transmission Grid Study emphasizes the necessity of hard thermal limits on lines (e.g., a standard 230 kV HVAC line peaking at roughly 400 MW) to prevent physical cable meltdowns, enforcing flow splintering across the grid.

## Mathematical Model

### Introduction
We formulate the power distribution problem as a Minimum Cost Network Flow model using a super-source initialization to manage variable generation caps. The mathematical model seeks an optimal distribution vector $x$ that minimizes the combined generation and transmission costs across the network graph.

### Definition and Terminology
- Sets:
  - $P$: Set of power plants.
  - $T$: Set of towns (demand centers).
  - $L$: Set of directed transmission lines $(i, j)$ connecting plant $i$ to town $j$.
  - $0$: A "super-source" node representing total pooled generation demand.
- Parameters:
  - $d_{ij}$: Geographic distance (in km) between plant $i$ and town $j$.
  - $c_{ij}$: Total transmission wheeling cost over edge $(i, j)$. We define $c_{ij} = 0.05 \times d_{ij}$ \$/MWh as a linear proxy for distance-based power loss and congestion.
  - $\text{Cost}_i$: Marginal cost of producing 1 MWh of electricity at plant $i$.
  - $\text{Cap}_i$: Maximum generation capacity limit (in MW) for plant $i$.
  - $D_j$: Peak power demand (in MW) for town $j$.
  - $U_{ij}$: Maximum thermal capacity limit (in MW) for transmission line $(i, j)$ (set to $400$ MW).
- Decision Variables:
  - $x_{ij}$: Flow of electricity (in MW) from node $i$ to node $j$.
  - $g_i$: Total power generated (in MW) by plant $i$, modeled as the flow $x_{0i}$ from the super-source to plant $i$.

### Formulation
Objective Function:
Minimize the total cost, which consists of the generation costs at the power plants and the transmission costs across the network lines.
$$ \min \sum_{i \in P} \text{Cost}_i \cdot g_i + \sum_{(i,j) \in L} c_{ij} \cdot x_{ij} $$

Constraints:
1. Supply and Demand Balance (Node Conservation):
   - The super source injects the exact total network demand: 
     $$ b(0) = -\sum_{j \in T} D_j $$
   - Power plants act as transshipment points (Generation in equals flow out): 
     $$ -g_i + \sum_{j \in T} x_{ij} = 0 \quad \forall i \in P $$
   - Towns must receive exactly their required demand: 
     $$ \sum_{i \in P} x_{ij} = D_j \quad \forall j \in T $$

2. Generation Capacity Limits:
   - A power plant cannot exceed its physical operational limit:
     $$ 0 \le g_i \le \text{Cap}_i \quad \forall i \in P $$

3. Transmission Thermal Limits:
   - The flow on any specific transmission line cannot exceed the thermal melt-down threshold of 400 MW:
     $$ 0 \le x_{ij} \le U_{ij} \quad \forall (i,j) \in L $$
     
## Results & Interpretation

### Merit-Order Dispatch 
The optimization strictly obeyed real-world power economics. Baseload plants with low marginal costs—Seabrook Station (Nuclear, $\$25$/MWh) and Comerford (Hydro, $\$15$/MWh)—were dispatched to their absolute maximum limits before mid-load gas plants were tapped to fulfill the final demand gaps.

### The Phase-Out of Coal
The solver determined that allocating $0$ MW to the Merrimack Station coal plant was the globally mathematically optimal strategy owing to its expensive $\$85$/MWh running cost. Strikingly, this precisely matches real-world events, as the Merrimack Station—New England's last coal-fired plant—has recently ceased operations due to identical economic pressures. This directly validates our operations research model against reality!

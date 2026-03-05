# Optimal Power Flow and Economic Dispatch: A Network Flow Approach ⚡🔌

Simulating the core economics and physical constraints of the modern electrical grid using Operations Research and Network Flow optimization. 

This project models the New Hampshire electric grid as a **Minimum Cost Network Flow** problem on a bipartite graph. We iteratively develop our model from a simple, unconstrained geographic transmission system into a fully constrained economic dispatch model with real-world transmission line bottlenecks.

## 🌍 Data Sources & Real-World Validation
Our model relies on highly credible, real-world data to ensure the network accurately reflects physical and economic realities:
- **Transmission Line Capacity (400 MW):** The thermal limit for our transmission lines is strictly informed by the **U.S. Department of Energy's National Transmission Grid Study**. standard 230 kV High-Voltage Alternating Current (HVAC) cables are capped to prevent sagging or melting.
- **Power Plant Costs & Generation Capacities:** Operational costs per MWh and maximum power outputs (Nuclear, Hydroelectric, Natural Gas, and Coal) are derived from the **U.S. Energy Information Administration's (EIA)** *Levelized Cost of Energy (LCOE)* reports. This enforces a realistic merit-order dispatch (cheap baseload power over expensive peaker plants).
- **Locations & Geography:** Demand centers represent 10 actual major cities in New Hampshire (e.g., Manchester, Nashua). Power plants represent 5 real regional facilities (e.g., Seabrook Nuclear, Merrimack Station Coal). Latitude and longitude coordinates were manually gathered via exact Google Maps searches.
- **Transmission Friction:** Straight-line geographic distances computed via the Haversine formula serve as the basis for our distance-based proxy transmission penalty (\$0.05 / km / MW), reflecting ohmic losses and congestion charges.

## 🗂️ Repository Structure

* `models.ipynb`: The core Jupyter Notebook containing the formulation and solution of our network flow models using the `gurobi_optimods` interface.
* `distance.ipynb` / `data.py`: Scripts used for preprocessing coordinate data and calculating the Haversine pairwise geographic distances between plants and towns.
* `*.csv`: Datasets containing plant capacities/costs, town demands, and plant-to-town distances.
* `writeup.md`: Detailed final project report with mathematical formulations and literary context.
* `requirements.txt`: Python package dependencies for reproducing the environment.

## ⚙️ Methodology
The core formulation seeks to minimize:
1. **Production Cost:** The marginal fuel cost of generating power varying wildly across power plant archetypes.
2. **Transmission Cost:** Wheeling tariffs and congestion proxies resulting from pushing electricity over long physical distances.

By treating the power sources and demand towns as nodes, and the transmission lines as edges, we use Gurobi to solve for the optimal distribution matrix.

## 🚀 Key Mathematical Results
1. **Merit-Order Emergence:** The solver natively maxes out cheap low-marginal-cost baseload power (Seabrook Nuclear, Comerford Hydroelectric) to 100% capacity before spinning up mid-load gas plants to satisfy remaining demand.
2. **Real-World Decommissioning:** Our optimal flow completely mathematically ignores the Merrimack Station due to the high marginal cost of Coal. This mirrors reality, as New England's last coal plant ceased major operations recently because it could not compete economically.
3. **Route Splintering:** Adding the 400 MW line thermal limit correctly forces the model to bypass network congestion, splintering flow across multiple parallel sources rather than using a singular "super-line" to feed major cities like Manchester.

## 🛠️ Installation & Usage

1. Clone the repository:
   ```bash
   git clone https://github.com/ewute/engs103-power-network-flow.git
   cd engs103-power-network-flow
   ```
2. Install dependencies (requires Gurobi, Pandas, etc.):
   ```bash
   pip install -r requirements.txt
   ```
3. Run the Jupyter Notebooks to view the models:
   ```bash
   jupyter notebook models.ipynb
   ```

***

## 🤖 AI Disclosure
Please note that portions of the documentation in this repository, including this README and sections of the final project writeup, were drafted and refined with the assistance of AI tools (like GitHub Copilot). All mathematical models, code logic, and data points, however, were manually directed, validated, and verified for accuracy.
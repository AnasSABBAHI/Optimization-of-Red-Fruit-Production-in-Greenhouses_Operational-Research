# Greenhouse Berry Production Optimization

##  Overview

This project presents a linear programming model designed to optimize the production of red fruits (raspberries and blackberries) in a complex of 24 greenhouses. The primary goal is to **maximize the total profit** by determining the most effective production scenario for each greenhouse.

A "scenario" is a unique combination of the fruit type, variety, planting method, and planting month. The model considers various operational constraints, such as greenhouse size, sector-specific limitations, and optional production limits, to provide a practical and data-driven solution for agricultural planning.

This repository is structured for academic use, providing a clear explanation of the mathematical model, the dataset, and the Python implementation using the `PuLP` library.

---

##  Table of Contents

- [Problem Description](#-problem-description)
- [The Mathematical Model](#-the-mathematical-model)
  - [Decision Variables](#decision-variables)
  - [Objective Function](#objective-function)
  - [Constraints](#constraints)
- [Dataset Description](#-dataset-description)
- [Repository Structure](#️-repository-structure)
- [How to Run the Code](#-how-to-run-the-code)
- [Results & Interpretation](#-results--interpretation)
- [Conclusion](#-conclusion)

---

##  Problem Description

A farm manages 24 greenhouses grouped into 6 distinct sectors. The management needs to decide which production strategy, or "scenario," to implement in each greenhouse for the upcoming season. There are 15 different scenarios available, each with its own cost, yield, and market price dynamics.

The challenge is to select a combination of scenarios that maximizes the farm's total profit while adhering to several operational constraints.

---

##  The Mathematical Model

We formulate this problem as a **Mixed-Integer Linear Programming (MILP)** model.

### Decision Variables

The core of the model is the binary decision variable $X_{ijk}$:

$X_{ijk} = \begin{cases} 1 & \text{if greenhouse } i \text{ is assigned to scenario } j \text{ with a start in week } k \\ 0 & \text{otherwise} \end{cases}$

Where:
- $i$ is the greenhouse index (1 to 24)
- $j$ is the scenario index (1 to 15)
- $k$ is the planting week index

### Objective Function

The objective is to **maximize the total profit**, which is the difference between total revenue (Chiffre d'Affaires, CA) and total variable costs (Charges Variables, CV).

$$ \text{Maximize} \sum_{i,j,k} (\text{Revenue}_{ijk} - \text{Cost}_{ij}) \cdot X_{ijk} $$

- **Revenue Calculation ($CA_{ijk}$):** The revenue for a given combination is the sum of weekly yields multiplied by the corresponding market price over the production period.

$$ CA_{ijk} = \text{Surface}_i \times \sum_{t'=1}^{37} \left( \text{Production}_{jt'} \times \text{Price}_{j, (k+t'+\text{Delay}_j)} \right) $$

- **Variable Cost Calculation ($CV_{ij}$):** The variable cost depends on the scenario and the greenhouse's surface area.

$$ CV_{ij} = \text{Surface}_i \times \text{Charges}_j $$

### Constraints

The model is subject to the following constraints:

1.  **One Scenario per Greenhouse**: Each greenhouse can be assigned exactly one scenario and one starting week.
    $$ \sum_{j,k} X_{ijk} = 1 \quad \forall i \in \{1, ..., 24\} $$

2.  **Sector-Specific Constraints**: Certain scenarios are restricted to specific sectors.
    - Scenarios 4 and 5 can **only** be planted in **Sector 6**.
    - Scenarios 12, 13, 14, and 18 can **only** be planted in **Sector 5** and in greenhouses with a surface area of **less than 2.87 hectares**.

3.  **Production Limit (Optional)**: Total production across all greenhouses can be capped at a predefined threshold to simulate limited market demand.
    $$ \sum_{i,j,k} (\text{TotalProduction}_{ijk}) \cdot X_{ijk} \leq \text{Threshold} $$

4.  **Scenario Consistency within Sectors (Optional)**: To streamline operations, all greenhouses within the same sector can be constrained to use the same scenario.

---

##  Dataset Description

The analysis is based on four key data files, which should be placed in a `data/` folder.

- **`Simulation.csv`**: Details on the 24 greenhouses, including the sector they belong to and their surface area in hectares. 
- **`Production.csv`**: Contains information for the 15 scenarios, including fruit type, variety, planting month, production delay (in weeks), and projected weekly yield (kg/hectare) for 37 weeks. 
- **`Prices.csv`**: Weekly market prices for raspberries (`Framboise`) and blackberries (`Mure`). 
- **`Charges_var.csv`**: The variable cost per hectare associated with each scenario. 

---

## 📊 Results & Interpretation

### Unconstrained Optimization

Without any production limits, the model achieves an **optimal profit of €9,433,967.5**. The optimal solution is:
- **Greenhouses 1-21**: Assigned to **Scenario 15** (YAZMIN Raspberry, TP), starting in **Week 18**.
- **Greenhouses 22-24 (Sector 6)**: Assigned to **Scenario 5** (YAZMIN Raspberry, MD+CB), starting in **Week 14**. 

This result shows a strong preference for Scenario 15 due to its high profitability under the given market conditions.

### Optimization with a Production Threshold

When a production limit is introduced (e.g., 600,000 kg), the model adjusts its strategy to stay within the cap.
- [cite_start]With a threshold of 600,000 kg, the optimal profit drops to **€8,849,775.0**. 
- [cite_start]The model substitutes the highly productive Scenario 15 with the less productive Scenario 2 in some greenhouses (18-21) to meet the constraint. 

[cite_start]This demonstrates a clear trade-off: **as the production threshold is tightened, the overall profit decreases** because the model is forced to choose less profitable scenarios. 

---

##  Conclusion

This project successfully demonstrates how mathematical optimization can be applied to complex agricultural planning problems. By modeling production scenarios, costs, prices, and operational constraints, we developed a powerful tool that provides clear, actionable recommendations for maximizing profitability.

The model's flexibility, allowing for the inclusion of constraints like production caps and sector-wide consistency, makes it a robust decision-support system for modern farming operations.
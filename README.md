# Production Shift & Workforce Planning for a Manufacturing Company | Python

**Author:** Bùi Xuân Bảo Duy (Kelvin)  
**Date:** September 2025  
**Tools Used:** Python, Gurobi  

## 🗂️ Table of Contents
1️⃣ [Context](#context)  
2️⃣ [Problem Description & Parameters](#problem-description--parameters)  
3️⃣ [Model Formulation](#model-formulation)  
4️⃣ [Solution Approach](#solution-approach)  

---

## 1️⃣ Context  

📘 **Main Context**
- The company operates with **80 workers** across **7 days** and **3 shifts per day**.
- **Manual scheduling in Excel** is currently used, which is time-consuming, error-prone & inflexible when workforce demand changes.  
- A faster, systematic, and logic-driven **optimization method** is required to ensure efficiency in planning, fairness in workload distribution, and compliance with labor policies.  

👥 **Target Audience**
- **Production Team** – needs stable manpower to meet daily output.  
- **Planning Team** – responsible for workforce allocation and scheduling.  
- **HR Team** – ensures labor rights, contracts, and fairness are respected.  

🎯 **Objective**
Develop an **optimized scheduling approach** that:  
- **Meets workforce demand** for every shift and day  
- Ensures **labor rights** and **contractual** compliance  
- **Balances workload** fairly across employees while **minimizing scheduling inefficiencies**  

🛠️ **Tools Used**
- **Excel**: for storing and reviewing final schedules after planning  
- **Python**: for data processing and modeling  
- **Gurobi Optimizer**: to solve the Mixed-Integer Programming model  

---

## 2️⃣ Problem Constraints & Parameters  

### 📅 Scheduling Horizon
- **Days**: Monday – Sunday (**7 days**)  
- **Shifts**: Morning (M), Afternoon (A), Evening (E)  

### 📊 Demand Table (workers required per shift)

| **Day** | **Morning** | **Afternoon** | **Evening** |
|---------|-------------|---------------|-------------|
| Mon     | 18          | 15            | 10          |
| Tue     | 20          | 20            | 10          |
| Wed     | 24          | 20            | 16          |
| Thu     | 16          | 14            | 10          |
| Fri     | 18          | 17            | 10          |
| Sat     | 25          | 24            | 12          |
| Sun     | 22          | 25            | 13          |

### 👥 Workforce
- **Total Employees**: 80  
  - **Full-time (FT)**: 60 workers  
    - ≤ 5 workdays per week (at least 2 days off)  
  - **Part-time (PT)**: 20 workers  
    - ≤ 3 workdays per week  

### ⚖️ Scheduling Rules
- Each worker can work **one shift per day** only  
- If a worker works **Evening (E)** on day *j*, they **cannot** work **Morning (M)** on day *j+1*  
- No worker may be assigned more than **2 Evening (E) shifts per week**  

### Objective
As a production planner, how can we assign each worker to each day and shift to: 
- Minimise costs.
- Satisfy the workforce demand every day and every shift.
- Respect labor rights and contract types.
- Balance workload across workers.

## 3️ Model Formulation
This part is presented in both business & technical language  
### Set
#### Business:  
- List of employees (80 people), 7 days in a week, and 3 shifts per day

#### Technical:  
- I is the set of the factory workforce, $I = \{1, 2, 3, \dots, 80\}$
- J is the set of weekday, $J = \{1, 2, 3, \dots, 7\}$
- K is the set of each shift within a day, $K = \{1, 2, 3\}$

### Parameters
#### Business:  
- Workforce demand for each shift and day
- Type of contract (full-time or part-time).

#### Technical:  
- $D_{jk}$ is the labor demand in day j at shift k  
- $ft_i = 1$ if worker $i$ is full-time, = $0$ if part-time  

### Decision variable
#### Business:  
- $x_{ijk} = 1$ if worker $i$ works on day $j$ at shift $k$, $0$ otherwise

#### Technical:  
- $x_{ijk} \in \{0,1\}, \; \forall i \in I, j \in J, k \in K

### Constraints
#### Business:  
- Each shift must have enough workers according to demand.
#### Technical:  
- For each shift $k$ in each day $j$, the number of laborers assigned must fulfill the demand:  
  $\sum_{i=1}^{80} x_{ijk} \geq D_{jk} \; \forall j \in J, \forall k \in K$
#### Business:  
- Each employee can work at most 1 shift per day.
#### Technical:  
- For each worker $i$ in each day $j$, the maximum number of shifts he/she can work is 1:  
  $\sum_{k=1}^{3} x_{ijk} \leq 1 \; \forall i \in I, \forall j \in J$
#### Business:  
- Full-time employees can work at most 5 shifts per week; part-time at most 3 shifts per week.
#### Technical:  
- For each worker $i$, the maximum number of shifts he/she can work is 5 if full-time, 3 if part-time:  
  $\sum_{j=1}^{7} \sum_{k=1}^{3} x_{ijk} \leq 5 \cdot ft_i + 3 \cdot (1 - ft_i) \; \forall i \in I$
#### Business:  
- Business: If an employee works the night shift today, they cannot work the morning shift the next day.
#### Technical:  
- For each worker $i$, in two consecutive days $j$ and $j+1$: if he/she works on the evening shift ($k = 3$) on day $j$, he/she cannot work on the morning shift ($k = 1$) on day $j+1$:  
  $x_{ij3} + x_{i(j+1)1} \leq 1 \; \forall i \in I, \forall j \in J$
#### Business:  
- Each employee can work at most 2 night shifts in a week.
#### Technical:  
- For each worker $i$, the maximum number of evening shifts ($k = 3$) he/she can work in a week is 2:  
  $\sum_{j=1}^{7} x_{ij3} \leq 2 \; \forall i \in I$  

### Objective function  
#### Business:  
- Minimize the total number of assigned shifts, optimize workforce usage.
#### Technical:  
- Minimizing the total workforce assigned:  
$\min \sum_{i=1}^{80} \sum_{j=1}^{7} \sum_{k=1}^{3} x_{ijk}$
---

## 4️⃣ Solution Approach
### Install & call Gurobi  
Install the Gurobi optimizer, activate the license, and import the gurobipy package in Python to build and solve optimization models.

```python
# Install gurobipy library
!pip install gurobipy

# Call gurobipy library
import gurobipy as gp

# Call pandas library
import pandas as pd
```

### Setup sets and parameters  
Define the sets (workers, days, shifts) and input parameters (workforce demand, contract type, max working days, shift limits) that describe the scheduling problem.  
```python
# Sets
I = 80 # Set of workforce (Labor 1, Labor 2, ..., Labor 80) (80 workers)
J = 7 # Set of weekday (Monday, Tuesday, ..., Sunday) (1 week 7 days)
K = 3 # Set of shift (Morning, Afternoon, Evening) (1 day 3 shift)

# Parameters
demand = [
    [18, 15, 10],  # Monday   = 43
    [20, 20, 10],  # Tuesday  = 50
    [24, 20, 16],  # Wednesday= 60
    [16, 14, 10],  # Thursday = 40
    [18, 17, 10],  # Friday   = 45
    [25, 24, 12],  # Saturday = 61
    [22, 25, 13]   # Sunday   = 60
]

ft = [1]*60 + [0]*20  # 80 workers with 60 full-time, 20 part-time)
```
### Setup models
Initialize a Gurobi optimization model, specify decision variables, and prepare the framework for adding constraints and the objective function.  

```python
# Create a gurobipy model
model = gp.Model("production_planning")

# Decision variable
x = model.addVars(I, J, K, vtype=gp.GRB.BINARY, name="Assigning staff i to day j at shift k")

# Objective function
model.setObjective(gp.quicksum(x[i, j, k] for i in range(I) for j in range(J) for k in range(K)), gp.GRB.MINIMIZE)

# Constraints

# Cons 1
model.addConstrs((gp.quicksum(x[i, j, k] for i in range(I)) >= demand[j][k] for j in range(J) for k in range(K)), name="demand fulfill")

# Cons 2
model.addConstrs((gp.quicksum(x[i, j, k] for k in range(K)) <= 1 for i in range(I) for j in range(J)), name="max 1 shift a day")

# Cons 3
model.addConstrs((gp.quicksum(x[i, j, k] for j in range(J) for k in range(K)) <= 5*ft[i] + 3*(1-ft[i]) for i in range(I)), name="5 days for full-time & 3 days for part-time")

# Cons 4
model.addConstrs((x[i, j, 2] + x[i, j+1, 0] <= 1 for i in range(I) for j in range(J-1)), name="if work on shift 3 today then no work on shift 1 tomorrow") #Bc Python starts from 0,1,2,...

# Cons 5
model.addConstrs((gp.quicksum(x[i, j, 2] for j in range(J)) <= 2 for i in range(I)), name="max 2 night shifts")
```

### Optimize model  
Run the Gurobi solver to process the defined model, find the optimal staff assignment, and generate solution outputs.  

```python
# Optimize model
model.optimize()
```

### Result
Extract and display the optimized staff assignment, showing how workers are scheduled across days and shifts while meeting all constraints.
![Image](https://github.com/kelvinduybui/Production-Planning/blob/main/Images/Production%20Planning%20result.png?raw=true)

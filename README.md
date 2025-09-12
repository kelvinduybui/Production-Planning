# Production-Planning

**Author:** Bùi Xuân Bảo Duy (Kelvin)  
**Date:** September 2025  
**Tools Used:** Python, Gurobi  

## 🗂️ Table of Contents
1️⃣ [Context](#context)  
2️⃣ [Problem Description & Parameters](#problem-description--parameters)  
3️⃣ [Model Formulation](#model-formulation)  
4️⃣ [Solution Approach](#solution-approach)  
5️⃣ [Visualization](#visualization)  
6️⃣ [Insights & Recommendations](#insights--recommendations)  

---

## 1️⃣ Context  

📘 **Main Context**
- The company operates with **100 workers** across **7 days** and **3 shifts per day**.
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
| Mon     | 25          | 21            | 17          |
| Tue     | 24          | 23            | 18          |
| Wed     | 27          | 21            | 19          |
| Thu     | 24          | 23            | 18          |
| Fri     | 25          | 22            | 17          |
| Sat     | 27          | 23            | 20          |
| Sun     | 23          | 23            | 19          |

### 👥 Workforce
- **Total Employees**: 100  
  - **Full-time (FT)**: 80 workers  
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
### Set
- I is the set of the factory workforce, $I = \{1, 2, 3, \dots, 100\}$
- J is the set of weekday, $J = \{1, 2, 3, \dots, 7\}$
- K is the set of each shift within a day, $K = \{1, 2, 3\}$

### Parameters
- $D_{jk}$ is the labor demand in day j at shift k  
- $ft_i = 1$ if worker $i$ is full-time, $0$ otherwise  

### Decision variable
- $x_{ijk} = 1$ if worker $i$ works on day $j$ at shift $k$, $0$ otherwise  

### Constraints
- For each shift k in each day j, the number of laborers to be assigned must fulfill the demand: $\sum_{i=1}^{100} x_{ijk} >= D_{jk}, \forall k \in K, \forall j \in J$
- For each labor i in each day j, the maximum number of shifts he/she can work is 1: $\sum_{k=1}^{3} x_{ijk} \leq 1, \forall i \in I, \forall j \in J$
- For each labor i, the maximum day and shift he/she can work is 5 if full-time, 3 if part-time: $\sum_{j=1}^{7} \sum_{k=1}^{3} x_{ijk} \leq 5 × ft_i + 3 × (1-ft_i), \forall i \in I$
- For each labor i in each 2 days j and j+1, if he/she works on evening shift (k = 3) today, he/she cannot work on the morning shift (k = 1) tomorrow: $x_{ij3} + x_{i(j+1)1} = 1, \forall j \in J, \forall j+1 \in J$
- For each labor i in each day j, the maximum number of night shifts he/she can work is 2: $\sum_{j=1}^{7} x_{ijk} \leq 2
---


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
- If a worker works **Evening (E)** on day *d*, they **cannot** work **Morning (M)** on day *d+1*  
- No worker may be assigned more than **2 Evening (E) shifts per week**  

### Key Question:
As a production planner, how can we assign each laborer to each day and each shift to minimize the cost, satisfy the demand, but still follow the rights?

# 3️ Model Formulation


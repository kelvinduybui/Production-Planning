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

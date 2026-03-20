# 🧪 QC Planner – Instrument Health Prediction Model
*A Machine Learning System to Build a Smart QC Instrument Scheduling Planner*

---

## 📌 Project Overview

QC Planner is a machine‑learning solution designed to predict the **health score** of QC laboratory instruments such as:

- HPLCs  
- Dissolution Apparatus (Disso)  
- UV Spectrophotometers  
- IR Instruments  

The goal is to help QC teams select the **best available instrument** for testing, reduce unplanned breakdowns, and plan maintenance proactively.

This model analyzes **10,000+ historical observations** per instrument and produces:

- **Final Health% (0–100)**  
- **Top Reason for Health Score**  
- **Most Recent Observation Date**  
- **One Row Per Instrument (Aggregated from Last 10 Observations)**  

---

## ⚙️ How It Works (High-level Workflow)

### **1. Input Data (Raw Dataset)**
Each row represents **one observation** for an instrument:

- Instrument ID  
- Date of Observation  
- Temperatures  
- Usage hours  
- Component age  
- Days since last PM  
- PM overdue flag  
- Environmental issues  
- Minor alarms  
- Past incidents  

### **2. Machine Failure Label (Synthetic Target)**
We generate `Machine_failure` (0/1) using domain-based rules:

- High temperature delta  
- High usage  
- Old components  
- Overdue PM  
- Many incidents  
- Room/power fluctuation issues  

### **3. Feature Engineering**
Derived features include:

- **Delta_T** = Instrument Temp – Room Temp  
- **PM_Risk** = Days_since_last_PM + Runs_since_PM  
- **Incident_Risk** = Minor_alarm_counts + Past_incidents_count  

### **4. Model Training (80/20 Split)**
We train a **Random Forest Classifier**:

- 80% → model training  
- 20% → testing & evaluation  

Performance metrics:

- Accuracy: ~98–99%  
- Precision: ~96%  
- Recall: ~96%  
- ROC‑AUC: ~0.99  

High accuracy is expected because labels are rule‑driven and consistent.

### **5. Health Score Generation**
We score each row:
Health% = (1 − FailureProbability) × 100

### **6. Reason Extraction**
Each row also gets a short explanation such as:

- “PM overdue”  
- “High usage”  
- “Old components”  
- “Many incidents”  
- “Env issues”  
- “Healthy”  

### **7. Aggregation (MOST IMPORTANT STEP)**
Since each instrument appears multiple times:

1. Sort by **Date of Observation**  
2. Select the **last 10 observations**  
3. Final Health% = **average** of those last 10 health scores  
4. Reason = **most common reason** in last 10 rows  
5. Keep the **most recent observation date**

This ensures the final score reflects *current* instrument behavior.

---

## 📄 Final Output (instrument_health_final_per_instrument.csv)

This file contains **one final row per instrument**:

| Instrument ID | Final Health% | Reason | Most Recent Date |
|---------------|---------------|--------|------------------|
| HPLC‑1        | 78            | PM overdue | 2026‑02‑18 |
| Disso‑3       | 42            | Many incidents | 2026‑02‑20 |
| UV Spec‑5     | 91            | Healthy | 2026‑02‑19 |
| IR‑2          | 55            | High usage | 2026‑02‑17 |

This output is ready to be consumed by **Power Apps** or **SharePoint** for a QC scheduling tool.

---

## 📊 Visualizations Included in Notebook

The notebook contains several helpful visuals:

- Feature Importance (Random Forest)  
- ROC Curve  
- Precision‑Recall Curve  
- Distribution of Health%  
- Health trend for sample instruments  
- Final instrument-level Health% bar chart  

---

## 🛠 How to Run This Project

### **1. Create virtual environment**
```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt

2. Run the notebook
Open:
01_train_and_score.ipynb

Run all cells to:

Train model
Generate outputs
Save final instrument health report


📦 Project Structure
instrument_health/
│
├── final_dataset.csv
├── 01_train_and_score.ipynb
├── instrument_health_model.pkl
├── instrument_health_final_per_instrument.csv
├── requirements.txt
├── .gitignore
└── .venv/   (ignored)

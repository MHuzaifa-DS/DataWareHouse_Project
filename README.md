# 🏥 Diabetic Patient Dashboard — Power BI

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Dataset](https://img.shields.io/badge/Dataset-UCI%20ML%20Repository-blue?style=for-the-badge)
![Records](https://img.shields.io/badge/Records-101%2C766-green?style=for-the-badge)
![Pages](https://img.shields.io/badge/Dashboard%20Pages-5-orange?style=for-the-badge)

> **Course:** Data Warehousing & Business Intelligence — April 2026  
> **Author:** Muhammad Huzaifa | SU92-BSDSM-F22-019

---

## 📌 Project Overview

This Power BI dashboard analyzes **10 years of diabetic inpatient encounter data** from 130 US hospitals (1999–2008). The goal is to explore readmission patterns, patient demographics, medication usage, and clinical indicators to identify high-risk patient groups.

| Metric | Value |
|--------|-------|
| 📋 Total Encounters | 101,766 |
| 🏨 Hospitals | 130 |
| 📊 Features | 50 |
| 📅 Time Period | 1999–2008 |
| 🎯 Target Variable | 30-Day Readmission |

---

## 📂 Repository Structure

```
📦 Diabetic-Patient-Dashboard
 ┣ 📊 Diabetic_Dashboard.pbix          # Main Power BI file
 ┣ 📄 diabetic_data.csv                # Source dataset (UCI ML Repository)
 ┣ 📑 README.md                        # This file
 ┗ 📁 assets/
    ┣ 🖼️ page1_executive_overview.png
    ┣ 🖼️ page2_demographics.png
    ┣ 🖼️ page3_readmission.png
    ┣ 🖼️ page4_clinical.png
    ┗ 🖼️ page5_medication.png
```

---

## 📊 Dashboard Pages

### Page 1 — Executive Overview
High-level KPIs and readmission outcome breakdown.

| KPI | Value |
|-----|-------|
| Total Encounters | 102K |
| 30-Day Readmit Rate | 11.16% |
| Not Readmitted | 55K (53.91%) |
| Missing A1C Data | 83.28% |

**Visuals:**
- Readmission outcome donut chart (Not Readmitted / After 30 Days / Within 30 Days)
- Encounters by admission type (Emergency dominates at ~60%)
- Gender split donut (Female 53.76% / Male 46.24%)

---

### Page 2 — Patient Demographics
Age, gender, and racial distribution across all encounters.

| KPI | Value |
|-----|-------|
| Largest Age Group | 70–80 yrs |
| Female Patients | 55K (53.76%) |
| Caucasian (Dominant) | 74.78% |
| Avg Patient Age | ~66 years |

**Visuals:**
- Encounters by age group (horizontal bar chart)
- Race/ethnicity donut chart
- Age × Gender clustered bar chart

> ⚠️ **Bias Note:** 74.78% Caucasian representation introduces bias risk for predictive modeling.

---

### Page 3 — Readmission Analysis
Identifying high-risk patient groups for 30-day hospital readmission.

| KPI | Value |
|-----|-------|
| Readmitted ≤30 Days | 11,357 (11.16%) |
| Not Readmitted | 54,864 (53.91%) |
| Highest Readmit Age Group | [20-30] |
| Discharge Type Highest Risk | Transfer patients |

**Visuals:**
- 30-Day readmit rate by age group
- 30-Day readmit rate by race
- Readmission outcome by discharge disposition (stacked bar)

> 💡 **Key Finding:** The [20-30] age group shows unexpectedly high readmission rates. Patients discharged to skilled nursing or other facilities have higher readmission risk than those discharged home.

---

### Page 4 — Clinical Indicators
A1C testing status, length of stay, lab procedures, and diagnosis complexity.

| KPI | Value |
|-----|-------|
| Missing A1C Tests | 83.28% |
| A1C Tested | Only 8.07% |
| Most Common Diagnosis | Code 428 (Heart Failure) |

**Visuals:**
- A1C testing status donut (Not Tested = 83.28%)
- 30-Day readmit rate by A1C result
- Total encounters by diag_1 (ICD-9 codes)
- Count of time_in_hospital

> 💡 **Key Finding:** Among tested patients, A1C >8 is a strong readmission predictor. Patients with high A1C have notably higher 30-day readmission rates.

---

### Page 5 — Medication Analysis
Usage patterns of 23 tracked diabetes drugs and their link to readmission outcomes.

| KPI | Value |
|-----|-------|
| Metformin Prescribed | 19.64% |
| Drugs Tracked | 23 |
| Med Changed Readmit Rate | Higher than steady |
| On Insulin Readmit Rate | ~10% |

**Visuals:**
- Top 10 drugs by prescription volume (table with dosage change status)
- 30-Day readmit rate by medication change (Ch vs No)
- No Insulin vs On Insulin readmission comparison

> 💡 **Key Finding:** Medication changes correlate with higher readmission — this reflects glycemic instability, not drug failure.

---

## 🛠️ Technical Implementation

### Tools Used
- **Power BI Desktop** — Dashboard development
- **Power Query Editor** — Data cleaning & transformation
- **DAX** — Custom measures and calculations

### Data Transformation Steps (Power Query)
1. Loaded `diabetic_data.csv` — 50 columns, 101K+ rows
2. Replaced all `?` values with `null`
3. Added custom column: **Readmission Category**
   ```
   if [readmitted] = "<30" then "Within 30 Days"
   else if [readmitted] = ">30" then "After 30 Days"
   else "Not Readmitted"
   ```
4. Added custom column: **Is30DayReadmit** (binary flag)
   ```
   if [readmitted] = "<30" then 1 else 0
   ```
5. Added custom column: **A1C_Status**
   ```
   if [A1Cresult] = null then "Not Tested" else [A1Cresult]
   ```

### DAX Measures Created

```dax
Total Encounters = COUNTROWS(diabetic_data)

30-Day Readmit Rate % =
DIVIDE(
    CALCULATE(COUNTROWS(diabetic_data), diabetic_data[readmitted] = "<30"),
    COUNTROWS(diabetic_data)
) * 100

Not Readmitted % =
DIVIDE(
    CALCULATE(COUNTROWS(diabetic_data), diabetic_data[readmitted] = "NO"),
    COUNTROWS(diabetic_data)
) * 100

Missing A1C % =
DIVIDE(
    CALCULATE(COUNTROWS(diabetic_data), diabetic_data[A1Cresult] = "None"),
    COUNTROWS(diabetic_data)
) * 100

Female Count =
CALCULATE(COUNTROWS(diabetic_data), diabetic_data[gender] = "Female")

Caucasian % =
DIVIDE(
    CALCULATE(COUNTROWS(diabetic_data), diabetic_data[race] = "Caucasian"),
    COUNTROWS(diabetic_data)
) * 100

Avg Age Numeric =
AVERAGEX(
    diabetic_data,
    SWITCH(diabetic_data[age],
        "[0-10)", 5, "[10-20)", 15, "[20-30)", 25,
        "[30-40)", 35, "[40-50)", 45, "[50-60)", 55,
        "[60-70)", 65, "[70-80)", 75, "[80-90)", 85,
        "[90-100)", 95, BLANK()
    )
)

Insulin Prescribed % =
DIVIDE(
    CALCULATE(COUNTROWS(diabetic_data), diabetic_data[insulin] <> "No"),
    COUNTROWS(diabetic_data)
) * 100

Metformin Prescribed % =
DIVIDE(
    CALCULATE(COUNTROWS(diabetic_data), diabetic_data[metformin] <> "No"),
    COUNTROWS(diabetic_data)
) * 100
```

---

## 📉 Data Quality & Limitations

| Field | Missing % | Handling Decision |
|-------|-----------|-------------------|
| Weight | 96.9% | ❌ Excluded from all analysis |
| Glucose Test | 95.4% | ❌ Excluded |
| A1C Result | 83.3% | ⚠️ Labeled "Tested Patients Only" in all visuals |
| Medical Specialty | 49.1% | ⚠️ Used with caution |
| Payer Code | 39.6% | ⚠️ Treated as own category |
| Race | 2.2% | ✅ Minor — usable |
| Diagnoses | 0.35% | ✅ Complete — fully usable |

### Dataset Strengths
- ✅ Large sample size (101K+) — low random bias risk
- ✅ Medication data 98% complete — 23 drugs tracked
- ✅ Strong target variable (readmission) for BI analysis

### Dataset Limitations
- ❌ A1C and Glucose fields largely missing — limits clinical analysis
- ❌ Weight field unusable (97% missing)
- ⚠️ Racial skew (74.8% Caucasian) — bias risk for ML modeling
- ⚠️ Data from 1999–2008 — may not reflect current clinical practices
- ⚠️ No timestamps per row — time-series analysis is limited

---

## 🔑 Key Findings

1. **11.16% 30-Day Readmission Rate** — Over 11,000 patients return within 30 days. This is the primary risk metric.
2. **Young Adults Are Surprisingly High Risk** — [20-30] age group has the highest readmission rate, possibly due to small sample size.
3. **Medication Change = Risk Signal** — Patients with changed medications have higher readmission, reflecting glycemic instability.
4. **83% Missing A1C is a Critical Gap** — High A1C (>8) strongly predicts readmission among tested patients.
5. **Emergency Admissions Dominate (60%)** — Suggests need for stronger outpatient/preventive care.
6. **Racial Imbalance = Bias Risk** — 74.78% Caucasian. Models trained on this data may underperform for minority groups.

---

## 📦 Dataset Source

- **Name:** Diabetes 130-US Hospitals (1999–2008)
- **Source:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/Diabetes+130-US+hospitals+for+years+1999-2008)
- **Citation:** Strack et al. (2014). Impact of HbA1c Measurement on Hospital Readmission Rates. *BioMed Research International*.

---

## 🚀 How to Open

1. Download `Diabetic_Dashboard.pbix`
2. Open with **Power BI Desktop** (free download from Microsoft)
3. If prompted for data source — point to `diabetic_data.csv` in the same folder
4. All measures and visuals will load automatically

---

## 📬 Contact

**Muhammad Huzaifa**  
Student ID: SU92-BSDSM-F22-019  
Course: Data Warehousing & Business Intelligence

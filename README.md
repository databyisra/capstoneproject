

---

# 💧 Water Potability Analysis – Data Analytics Case Study

**By: Isra S.**

---

## Project Overview

This case study analyzes the potability of water samples based on various chemical and physical attributes. The goal is to identify the distribution of safe vs. unsafe water, determine key factors affecting potability (especially pH), and uncover trends across hardness levels.

---

## 1. Ask: Define the Problem and Research Questions

**Objective**: Assess the quality of water samples and identify the most influential factors affecting potability.

**Research Questions**:

1. Question 1: What is the distribution of safe vs. unsafe water samples?
2. Question 2: What key factors influence water potability the most? (Focus on pH)
3. Question 3: What trends can we observe across different hardness levels?

---

## 2. Prepare: Understand the Data

**Source**: [Kaggle – Water Quality: Drinking Water Potability Dataset](https://www.kaggle.com/datasets/adityakadiwal/water-potability)
**Sample Size**: 3,276 water samples

### Key Parameters

| Parameter           | Description                                            |
| ------------------- | ------------------------------------------------------ |
| **pH**              | Measures acidity/alkalinity (WHO safe range: 6.5–8.5)  |
| **Hardness**        | Calcium & magnesium salts; affects taste and usability |
| **TDS**             | Total Dissolved Solids; limit: up to 500 mg/L          |
| **Chloramines**     | Disinfectant; safe up to 4 mg/L                        |
| **Sulfates**        | Naturally occurring (3–30 mg/L typical)                |
| **Conductivity**    | Indicates ionic content; WHO limit: < 400 μS/cm        |
| **Organic Carbon**  | Measures organic compounds                             |
| **Trihalomethanes** | Chlorination byproduct; safety limit: 80 ppm           |
| **Turbidity**       | Water clarity; max recommended: 5 NTU                  |
| **Potability**      | Binary target (1 = potable, 0 = not potable)           |

---

## 3. Process: Data Cleaning

Missing values in the `potability` field were removed, and overall data integrity was verified.

```sql
CREATE OR REPLACE TABLE `bold-artifact-458415-p5.WaterMetrics.WData_cleaned` AS
SELECT * FROM `bold-artifact-458415-p5.WaterMetrics.WData`
WHERE potability IS NOT NULL;
```

---

## 4. Analyze: SQL-Based Exploratory Queries

### Q1: Distribution of Potable vs Non-Potable Water

```sql
SELECT 
  potability,
  COUNT(*) AS sample_count,
  ROUND(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 2) AS percentage
FROM `bold-artifact-458415-p5.WaterMetrics.WData_cleaned`
GROUP BY potability
ORDER BY potability;
```

* **Result**: \~61% unsafe, \~39% safe

---

### Q2: Influence of pH Range on Potability

```sql
SELECT
  CASE
    WHEN pH BETWEEN 6.5 AND 8.5 THEN '6.5-8.5 (WHO recommended)'
    WHEN pH > 8.5 THEN 'Above 8.5'
    WHEN pH < 6.5 THEN 'Below 6.5'
    ELSE 'Unknown'
  END AS pH_range,
  COUNT(*) AS sample_count,
  ROUND(AVG(potability), 4) AS avg_potability
FROM `bold-artifact-458415-p5.WaterMetrics.WData_cleaned`
GROUP BY pH_range
ORDER BY sample_count DESC;
```

* **Insight**: WHO-recommended pH samples have \~42% potability rate—higher than other ranges.

---

### Q3: Potability Across Hardness Levels

```sql
SELECT
  CASE
    WHEN Hardness BETWEEN 151 AND 300 THEN '151-300'
    WHEN Hardness BETWEEN 50 AND 150 THEN '50-150'
    WHEN Hardness > 300 THEN 'Above 300'
    WHEN Hardness < 50 THEN 'Below 50'
    ELSE 'Unknown'
  END AS hardness_range,
  COUNT(*) AS sample_count,
  ROUND(AVG(potability), 4) AS avg_potability
FROM `bold-artifact-458415-p5.WaterMetrics.WData_cleaned`
GROUP BY hardness_range
ORDER BY sample_count DESC;
```

* **Insight**: Most samples have moderate hardness (151–300 mg/L), with \~38% potability.

---

## 5. Share: Visual Insights

Detailed dashboards are available via Tableau. See next sheet or use the following links:

* 🔗 [Distribution of Safe vs Unsafe Water Samples](https://public.tableau.com/views/DistributionofSafevsUnsafeWaterSamples/Sheet13)
* 🔗 [Water Potability by pH Range](https://public.tableau.com/views/WaterPotabilityAcrossHardnessRanges/Sheet1)
* 🔗 [Hardness Range Potability Analysis](https://public.tableau.com/views/WaterPotabilityAcrossHardnessRanges/Sheet1)

---

## 6. Act: Final Recommendations

### 🔍 Key Takeaways

* **61%** of samples are unsafe → major quality concern
* **pH within 6.5–8.5** → higher potability (\~42%)
* **Hardness (151–300 mg/L)** → majority category, but only moderate potability

### 💡 Recommendations

1. **pH Monitoring**: Target samples with pH outside the 6.5–8.5 range for intervention
2. **Optimize Hardness**: Investigate sources and effects of mid-range hardness
3. **Comprehensive Testing**: Expand focus to chloramines, sulfates, turbidity, etc., for safety insights

---


```

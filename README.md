# Beyond Ripeness Classification: A Survival Analysis Framework for Hass Avocado Shelf-Life Modelling

![Research](https://img.shields.io/badge/Research-Survival%20Analysis-green)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Reproducibility](https://img.shields.io/badge/Reproducibility-Enabled-orange)
![Dataset](https://img.shields.io/badge/Dataset-Mendeley%20Data-lightgrey)
---

## Overview

This repository provides the computational workflow accompanying a study on survival-based shelf-life modelling of Hass avocado ripening dynamics.

The Hass Avocado Ripening Photographic Dataset was originally developed for image-based ripeness assessment, where individual observations were assigned to predefined ripening stages. In this work, the longitudinal structure of the dataset is reinterpreted as a time-to-event modelling problem.

Rather than asking:

> "What ripening stage is the fruit currently in?"

This study investigates:

> "How long does an individual fruit remain before reaching the defined shelf-life endpoint?"

The workflow converts repeated fruit-level ripening observations into longitudinal trajectories and applies survival-analysis methods to estimate shelf-life progression under different storage conditions.

The analysis includes non-parametric survival estimation, accelerated failure time modelling, interval-censored survival analysis, restricted mean survival time estimation, and sensitivity analyses to evaluate the robustness of estimated shelf-life trajectories.

This repository is intended to support transparent and reproducible analysis of longitudinal agricultural imaging datasets for time-to-event shelf-life modelling.

---

## Research Question

Can longitudinal fruit-image observations be transformed into a reproducible shelf-life model using survival analysis?

---

## Key Idea

Most ripening studies using image-based datasets focus on identifying the current maturity state of a fruit:

> "What ripening stage is the fruit currently in?"

This repository explores a complementary question:

> "How long will an individual fruit remain before reaching the defined shelf-life endpoint?"

To address this, longitudinal ripening observations are transformed from image-level classifications into a fruit-level time-to-event framework:

Image-level ripening observations
        ↓

Fruit-level longitudinal trajectories
        ↓

Time-to-event survival dataset
        ↓

Survival modelling and uncertainty estimation
        ↓

Shelf-life estimation

This transformation enables existing longitudinal agricultural imaging datasets to be analysed not only for ripeness classification, but also for shelf-life dynamics and time-dependent quality progression.

<img width="1122" height="1402" alt="Avocado Survival Analysis Workflow" src="https://github.com/user-attachments/assets/21568841-8876-40c8-acdc-5de7cb25ba70" />

---

# Dataset

## Hass Avocado Ripening Photographic Dataset

This study uses the publicly available **Hass Avocado Ripening Photographic Dataset** from Mendeley Data:

Dataset source:

https://data.mendeley.com/datasets/3xd9n945v8/1


The dataset contains longitudinal observations of individual Hass avocados during postharvest ripening progression.

### Dataset characteristics

- **478 Hass avocado fruits**
- **14,710 photographic observations**
- **Three storage conditions**
  - T10: 10 °C
  - T20: 20 °C
  - Ambient temperature
- **Daily longitudinal observations during ripening**
- **Two photographed sides per fruit**
- **Five predefined ripening stages**


## Original Dataset Purpose

The dataset was originally developed for image-based avocado ripeness assessment, where individual observations were assigned to predefined ripening stages for classification and deep learning applications.

## Use in This Repository

Rather than treating ripening stages only as categorical image labels, this repository exploits the longitudinal structure of the dataset to reconstruct fruit-level ripening trajectories.

The repeated observations are transformed into a time-to-event framework, where the transition to the shelf-life endpoint is modelled using survival-analysis methods.

This represents a secondary analysis of the publicly available dataset and demonstrates how longitudinal agricultural imaging datasets can support shelf-life modelling beyond conventional ripeness classification.

---
# Methodology

## 1. Fruit-Level Longitudinal Reconstruction

The original dataset contains repeated image-based ripening observations collected throughout the postharvest ripening process. To enable time-to-event analysis, image-level observations were transformed into fruit-level longitudinal trajectories.

Each observation was uniquely identified using:

Storage condition + Fruit ID + Observation day


This transformation converted repeated photographic observations into daily ripening trajectories for individual fruits.

Because each avocado was photographed from two opposite sides, ripening stages may differ slightly between observations due to surface-level variation. Therefore, three aggregation strategies were evaluated:

### Primary analysis

**Maximum ripening stage per fruit-day**

The maximum observed stage was selected as the primary representation because it captures the most advanced ripening state observed for each fruit-day.

### Sensitivity analyses

Alternative aggregation strategies were evaluated:

- Mean ripening stage per fruit-day
- Minimum ripening stage per fruit-day

The purpose of these analyses was to determine whether shelf-life estimates were dependent on the method used to combine observations from the two fruit sides.

---

# 2. Shelf-Life Endpoint Definition

## Primary Endpoint

### Stage 4 Ripening Index

Stage 4 was selected as the primary shelf-life endpoint because it represents the transition beyond the optimal ripeness stage defined by the dataset.

The survival outcome was formulated as a time-to-event problem:
Time:
Days after storage initiation
Event:
Fruit reaches Stage 4

Fruit observations that did not reach Stage 4 during the monitoring period were treated as right-censored observations.

This formulation allows incomplete ripening trajectories to be incorporated without assuming that the final ripening time was observed.

---

# Statistical Framework

## Survival Estimation

Non-parametric survival methods were used to characterize ripening progression under different storage conditions.

Implemented methods:

- Kaplan-Meier survival estimation
- Global log-rank test
- Pairwise storage-condition comparisons

Kaplan-Meier estimation was used to describe the probability that fruits remained below the shelf-life endpoint over time, while log-rank testing evaluated differences between storage trajectories.

---

### Weibull Accelerated Failure Time (AFT) Model

The Weibull AFT model was used as the primary effect interpretation model because it provides directly interpretable time ratios describing acceleration or delay in shelf-life progression between storage conditions.

### Interval-Censored Weibull Model

Because ripening stage was observed only at discrete daily intervals, the exact transition time to Stage 4 was unknown.

For example:


Day 12 → Stage 3
Day 13 → Stage 4

The true transition occurred within:


12 < T ≤ 13

Interval-censored modelling was therefore performed as a sensitivity analysis to evaluate whether uncertainty in event timing influenced conclusions.

---

## Restricted Mean Survival Time (RMST)

Restricted Mean Survival Time was calculated to provide an absolute measure of expected shelf-life within the observed follow-up period.

RMST estimation was complemented with bootstrap confidence intervals to quantify uncertainty around estimated survival times.

---

## Sensitivity and Robustness Analysis

The workflow included multiple robustness evaluations:

- Fruit-side aggregation sensitivity
  - Maximum stage
  - Mean stage
  - Minimum stage

- Censoring inspection
  - Identification of censored fruits
  - Last observed ripening stage
  - Last observed day

- Short follow-up censoring sensitivity
  - Exclusion of day-1-only censored observations

- Alternative endpoint analysis
  - Stage 5 sensitivity analysis

- Conceptual causal structure evaluation
  - Storage condition
  - Storage chamber
  - Ripening progression
  - Shelf-life endpoint

---
## Parametric Survival Modelling

The following parametric survival models were implemented:

### Weibull Accelerated Failure Time (AFT) Model

The Weibull AFT model was used as the main effect-interpretation model because it provides directly interpretable **time ratios**, allowing shelf-life progression under different storage conditions to be compared on an absolute time scale.

### Interval-Censored Weibull Model

Because ripening stages were observed only at discrete daily time points, the exact transition time to Stage 4 was not directly observed.

For example:

Day 12 → Stage 3  
Day 13 → Stage 4  

Therefore, the true transition time lies within:

`12 < T ≤ 13`

To account for this timing uncertainty, an interval-censored Weibull survival model was used as an important sensitivity analysis and is treated as the more conservative time-ratio estimate.

---

## Absolute Shelf-Life Comparison

Restricted Mean Survival Time (RMST) was calculated to provide an absolute estimate of expected shelf-life within the observed follow-up period.

The RMST analysis included:

- RMST estimation
- Bootstrap confidence intervals
- Pairwise RMST difference estimation

---

# Robustness and Sensitivity Analysis

The workflow includes the following robustness checks:

✅ **Duplicate observation verification**  
- Confirmation of fruit-day consistency  
- Verification that each fruit-day contains the expected paired observations  

✅ **Fruit-side aggregation sensitivity**  
- Maximum stage per fruit-day  
- Mean stage per fruit-day  
- Minimum stage per fruit-day  

✅ **Censoring inspection**  
- Identification of censored fruits  
- Extraction of last observed day  
- Extraction of last observed ripening stage  

✅ **Short follow-up censoring sensitivity**  
- Day-1-only censored observations were removed  
- Survival estimates were re-evaluated to assess the effect of potentially uninformative short follow-up  

✅ **Alternative endpoint sensitivity analysis**  
- Stage 5 used as an alternative shelf-life endpoint  

✅ **Interval-censored survival modelling**  
- Daily observation uncertainty explicitly incorporated into time-to-event modelling  

✅ **Conceptual DAG evaluation**  
- Storage regime  
- Chamber context  
- Ripening progression  
- Shelf-life endpoint  

---

# Main Results

## Median Time to Stage 4

Kaplan-Meier median shelf-life estimates showed clear separation between storage conditions:

| Storage Condition | Median Shelf-Life |
|------------------|------------------|
| T10 (10 °C) | 17 days |
| T20 (20 °C) | 7 days |
| Ambient | 7 days |

These results indicate that fruit stored at 10 °C remained below the Stage 4 shelf-life endpoint substantially longer than fruit stored at 20 °C or under ambient conditions.

---

<img width="2539" height="1638" alt="kaplan_meier_curve" src="https://github.com/user-attachments/assets/24e0627c-f8af-464a-b14e-c4041ac43483" />


 
**Kaplan-Meier survival curves showing delayed progression toward Stage 4 under 10 °C storage compared with 20 °C and ambient conditions.**

---

# Weibull Accelerated Failure Time Results

## Primary interpretation from interval-censored modelling

Relative to T10 storage, the interval-censored Weibull model indicated substantially shorter time to Stage 4 under warmer conditions.

| Storage Condition | Time Ratio (approx.) |
|------------------|----------------------|
| T20 | 0.41 |
| Ambient | 0.40 |

Interpretation:

Fruits stored at 20 °C and under ambient conditions reached the Stage 4 shelf-life endpoint in roughly **40% of the time** required under 10 °C storage.

This result was consistent with the standard Weibull AFT model and supports the conclusion that warmer storage regimes were associated with faster ripening progression.

> Note: The interval-censored model is emphasized here because the exact event time was not directly observed between daily assessments.

---

## T20 vs Ambient Comparison

Pairwise comparison between T20 and ambient storage showed no clear evidence of a difference in Stage 4 progression timing.

- **Log-rank test (T20 vs Ambient): p = 0.31**

This suggests that, within this dataset, 20 °C and ambient storage produced similar ripening trajectories for the Stage 4 endpoint.

---

# Restricted Mean Survival Time

RMST estimates also supported longer shelf-life under 10 °C storage.

| Storage Condition | RMST (days) |
|------------------|-------------|
| T10 | 16.17 |
| T20 | 6.80 |
| Ambient | 6.71 |

Bootstrap confidence intervals were calculated to quantify uncertainty around these RMST estimates and around pairwise RMST differences.

Overall, RMST results were consistent with both the Kaplan-Meier and AFT analyses, showing that 10 °C storage preserved shelf-life markedly longer than the warmer storage conditions.

---

<img width="2100" height="1407" alt="shelf_life_distribution" src="https://github.com/user-attachments/assets/01be1b6c-bd1e-4602-81b7-65ae8b9b18f0" />

*Observed time-to-event patterns across storage conditions. Survival modelling was used as the primary analysis framework because it accounts for censored observations.*
---

# Sensitivity Results

## Side Aggregation Sensitivity

All three fruit-side aggregation approaches produced identical median shelf-life estimates:

| Aggregation Strategy | T10 | T20 | Ambient |
|----------------------|-----|-----|---------|
| Maximum | 17 | 7 | 7 |
| Mean | 17 | 7 | 7 |
| Minimum | 17 | 7 | 7 |

This indicates that the overall shelf-life conclusions were robust to the method used to combine the two photographed fruit sides.

---

## Short Follow-Up Censoring Sensitivity

Because some censored fruits had extremely short follow-up, an additional sensitivity analysis excluded day-1-only censored observations.

The purpose of this step was to assess whether unusually short monitoring periods influenced survival estimates. The overall conclusion remained unchanged: 10 °C storage was associated with substantially longer time to the Stage 4 endpoint.

---

## Stage 5 Endpoint Sensitivity

A secondary survival analysis using **Stage 5** as the endpoint showed the same overall pattern:

- 10 °C storage maintained longer ripening trajectories
- 20 °C and ambient storage showed faster progression
- The relative ordering of storage conditions remained unchanged

This supports the robustness of the primary Stage 4 findings.

---

<img width="2539" height="1638" alt="KM_stage5_sensitivity" src="https://github.com/user-attachments/assets/57b42ef6-407f-41fc-b601-6d785f41daea" />


 
**Kaplan-Meier sensitivity analysis using Stage 5 as an alternative shelf-life endpoint. The overall ranking of storage conditions remained consistent with the primary Stage 4 analysis.**

---

# Interpretation

Taken together, the Kaplan-Meier, RMST, standard Weibull AFT, and interval-censored survival analyses all support the same practical conclusion:

- **10 °C storage was associated with substantially longer shelf-life**
- **20 °C and ambient storage showed broadly similar ripening trajectories**
- **The main findings were robust across multiple sensitivity analyses**

Because this is a secondary analysis of an existing dataset, the results should be interpreted as **associations between storage regime and observed ripening progression**, rather than as definitive causal effects.

---

# Repository Structure

```text
avocado-survival-analysis/
│
├── README.md
│
├── notebooks/
│   └── 01_Avocado_Survival_Analysis.ipynb
│
├── figures/
│   ├── workflow_diagram.png
│   ├── kaplan_meier_curve.png
│   ├── shelf_life_distribution.png
│   ├── KM_stage5_sensitivity.png
│   └── avocado_conceptual_DAG.png
│
├── results/
│   ├── RMST_with_bootstrap_CI.csv
│   ├── aft_results.csv
│   ├── interval_censored_weibull_AFT_results.txt
│   ├── KM_median_survival.csv
│   └── final_analysis_summary_revised.csv
│
├── requirements.txt
│
└── LICENSE

---

# Reproducibility

This repository is designed to provide a transparent and reproducible implementation of the survival-analysis workflow.

## Installation

Clone the repository:

```bash
git clone https://github.com/yourusername/avocado-survival-analysis.git

cd avocado-survival-analysis

Install the required dependencies:
pip install -r requirements.txt


The notebook contains the complete workflow, including:
- dataset preparation,
- fruit-level trajectory reconstruction,
- survival dataset generation,
- Kaplan-Meier analysis,
- Weibull AFT modelling,
- interval-censored survival analysis,
- RMST estimation,
- sensitivity analyses.
Data Availability
The original dataset is not redistributed in this repository.
The Hass Avocado Ripening Photographic Dataset can be downloaded from the official source: https://data.mendeley.com/datasets/3xd9n945v8/1

Limitations:
This repository represents a secondary analysis of an existing public dataset.
Important limitations include:
- No new avocado storage experiment was performed.
- Detailed information regarding chamber-level replication and treatment allocation was unavailable.
- Storage conditions should therefore be interpreted as associated with observed ripening trajectories rather than definitive causal effects.
- Biological quality measurements, including firmness, chemical composition, and sensory evaluation, were not available in the dataset.

Relationship to the Original Dataset Study:
The original Hass Avocado Ripening Photographic Dataset was developed primarily for image-based ripeness assessment and classification. This repository extends the analytical use of the dataset by treating ripening progression as a time-to-event survival problem. The objective is not to replace image classification approaches, but to provide an additional statistical framework for modelling shelf-life dynamics from longitudinal agricultural imaging data.

# Citation

If you use this workflow, please cite the repository, dataset, and original publication.

## Repository

**Avocado Survival Analysis Framework**

GitHub Repository:

https://github.com/SumaiyaData/avocado-survival-analysis


## Dataset

Xavier, Pedro; Rodrigues, Pedro; L. M. Silva, Cristina (2024).

**"Hass Avocado Ripening Photographic Dataset."**

Mendeley Data, V1.

https://doi.org/10.17632/3xd9n945v8.1


## Related Publication

Xavier P., Rodrigues P.M., Silva C.L.M. (2024).

**"Shelf-Life Management and Ripening Assessment of Hass Avocado Using Deep Learning Approaches."**

Foods, 13(8), 1150.


---


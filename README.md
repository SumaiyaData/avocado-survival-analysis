# Beyond Ripeness Classification: A Survival Analysis Framework for Hass Avocado Shelf-Life Modelling

![Research](https://img.shields.io/badge/Research-Survival%20Analysis-green)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Reproducible](https://img.shields.io/badge/Reproducible-Workflow-orange)

---

## Overview

This repository contains the complete reproducible workflow for:

**"Beyond Ripeness Classification: A Reproducible Survival Analysis Framework for Modelling Hass Avocado Shelf-Life Dynamics from Longitudinal Imaging Data"**

This project presents a survival-analysis framework for transforming longitudinal avocado ripening observations into fruit-level time-to-event data for shelf-life modelling.

The Hass Avocado Ripening Photographic Dataset was originally developed for image-based ripeness assessment. Instead of treating ripening observations only as classification labels, this work investigates their temporal structure by modelling:

> **How long does an individual fruit remain before reaching the end-of-shelf-life stage?**

The repository demonstrates how publicly available agricultural imaging datasets can be converted into statistically rigorous survival datasets and analysed using uncertainty-aware survival modelling approaches.

---

## Research Question

Can longitudinal fruit-image observations be transformed into a statistically rigorous shelf-life model using survival analysis?

---

## Key Idea

Traditional ripeness analysis asks:
What ripening stage is the fruit currently in?

This project asks:
How long will the fruit remain before reaching the shelf-life endpoint?


The framework transforms:
Image-level ripening observations
        ↓

Fruit-level longitudinal trajectories
        ↓

Time-to-event survival dataset
        ↓

Shelf-life estimation with uncertainty


---

# Dataset

## Hass Avocado Ripening Photographic Dataset

Dataset source:

Mendeley Data:

https://data.mendeley.com/datasets/3xd9n945v8/1


Dataset characteristics:

- 478 Hass avocado fruits
- 14,710 photographic observations
- Three storage conditions:
  - T10: 10 °C
  - T20: 20 °C
  - Ambient temperature
- Daily longitudinal observations
- Two photographed sides per fruit
- Five ripening stages


The dataset was originally developed for avocado ripeness classification and deep learning applications. This repository performs a secondary survival-analysis investigation using the longitudinal structure of the dataset.

---

# Workflow

## Add Figure Here

**Recommended image:**
`workflow_diagram.png`

Place it after this section.

The workflow should show:
Avocado Images
    ↓

Fruit-day aggregation
    ↓

Ripening trajectory reconstruction
    ↓

Time-to-event dataset
    ↓

Survival modelling
    ↓

Shelf-life estimation

---

# Methodology

## 1. Fruit-Level Longitudinal Reconstruction

The original image-level observations were transformed into fruit-level daily trajectories using:


Storage condition + Fruit ID + Observation day


Because each avocado was photographed from two sides, three aggregation strategies were evaluated:

### Primary analysis

Maximum ripening stage per fruit-day

### Sensitivity analyses

- Mean ripening stage
- Minimum ripening stage

The purpose was to evaluate whether shelf-life estimates depended on the selected image-side aggregation strategy.

---

# 2. Shelf-Life Endpoint Definition

## Primary Endpoint

### Stage 4 Ripening Index

Stage 4 was selected as the primary shelf-life endpoint because it represents the transition beyond optimal ripeness.

The survival outcome was defined as:


Time:
Days after storage
Event:
Fruit reaches Stage 4


Fruit that did not reach Stage 4 during observation were treated as right-censored.

---

# Statistical Framework

## Survival Estimation

The following non-parametric survival methods were implemented:

- Kaplan-Meier survival estimation
- Global log-rank test
- Pairwise storage comparison


---

## Parametric Survival Modelling

The following models were implemented:

### Weibull Accelerated Failure Time (AFT)

Used as the primary effect interpretation model because it provides directly interpretable time ratios.

### Interval-Censored Weibull Model

Used because the exact Stage 4 transition time is unknown between daily observations.

Example:
Day 12 → Stage 3
Day 13 → Stage 4
True transition:
12 < T ≤ 13


---

## Absolute Shelf-Life Comparison

Restricted Mean Survival Time (RMST) was calculated with:

- RMST estimation
- Bootstrap confidence intervals

---

# Robustness and Sensitivity Analysis

The workflow includes:

✅ Duplicate observation verification

✅ Fruit-side aggregation sensitivity

- Maximum stage
- Mean stage
- Minimum stage


✅ Censoring inspection

- Identification of censored fruits
- Last observed day and ripening stage


✅ Short follow-up censoring sensitivity

Day-1-only censored observations were removed and survival estimates were re-evaluated.


✅ Stage 5 endpoint sensitivity analysis


✅ Interval-censored survival modelling


✅ Conceptual causal diagram evaluation

---

# Main Results

## Median Time to Stage 4

| Storage Condition | Median Shelf-Life |
|------------------|------------------|
| T10 (10 °C) | 17 days |
| T20 (20 °C) | 7 days |
| Ambient | 7 days |


---

## Add Figure Here

**Recommended image:**
figures/kaplan_meier_curve.png



Place the Kaplan-Meier curve here.

Caption:

**Kaplan-Meier survival curves showing delayed progression toward Stage 4 under 10 °C storage compared with 20 °C and ambient conditions.**

---

# Weibull Accelerated Failure Time Model

Relative to T10 storage:

| Storage Condition | Time Ratio |
|------------------|-----------|
| T20 | 0.43 |
| Ambient | 0.42 |


Interpretation:

Warmer storage conditions accelerated progression toward Stage 4.

Fruits stored at 20 °C and ambient conditions reached the shelf-life endpoint in approximately 42–43% of the time required under 10 °C storage.

---

# Restricted Mean Survival Time

RMST estimates:

| Storage Condition | RMST (days) |
|------------------|-------------|
| T10 | 16.17 |
| T20 | 6.80 |
| Ambient | 6.71 |


Bootstrap confidence intervals were calculated to quantify uncertainty in RMST estimates.

---

## Add Figure Here

**Recommended image:**
figures/shelf_life_distribution.png




Caption:

**Distribution of observed time-to-Stage 4 across storage conditions.**

---

# Sensitivity Results

## Side Aggregation Sensitivity

All aggregation approaches produced identical median shelf-life estimates:

| Aggregation | T10 | T20 | Ambient |
|-------------|-----|-----|---------|
| Maximum | 17 | 7 | 7 |
| Mean | 17 | 7 | 7 |
| Minimum | 17 | 7 | 7 |


This demonstrates that conclusions were robust to alternative fruit-side aggregation strategies.

---

## Stage 5 Endpoint Sensitivity

A secondary analysis using Stage 5 as the endpoint showed the same overall trend:

- 10 °C storage maintained longer ripening progression.
- 20 °C and ambient storage showed faster transition.

---

## Add Figure Here

**Recommended image:**


figures/KM_stage5_sensitivity.png



Caption:

**Kaplan-Meier sensitivity analysis using Stage 5 as an alternative endpoint.**

---

# Repository Structure


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

## Installation

Clone the repository:

```bash
git clone https://github.com/yourusername/avocado-survival-analysis.git

cd avocado-survival-analysis

Install required packages:
pip install -r requirements.txt

Run:
notebooks/01_Avocado_Survival_Analysis.ipynb

Data Availability
The original dataset is not redistributed in this repository.
Users should download the dataset from the official source:
Mendeley Data:
https://data.mendeley.com/datasets/3xd9n945v8/1
After downloading, follow the notebook instructions for dataset placement.
Limitations
This repository represents a secondary analysis of an existing public dataset.
Important limitations:
- No new avocado storage experiment was performed.
- Detailed chamber-level replication information was unavailable.
- Storage conditions should be interpreted as associated with ripening trajectories rather than definitive causal effects.
- Biological measurements such as firmness, chemical composition, and sensory evaluation were not available.
Relationship to the Original Dataset Study
The original Hass Avocado Ripening Photographic Dataset was developed primarily for image-based ripeness assessment.
This repository extends the analytical use of the dataset by treating ripening progression as a time-to-event survival problem.
The objective is not to replace image classification approaches, but to provide an additional statistical framework for shelf-life modelling from longitudinal agricultural observations.
Citation
If you use this workflow, please cite:
Repository
Avocado Survival Analysis Framework.
GitHub Repository:
https://github.com/yourusername/avocado-survival-analysis
Dataset
Hass Avocado Ripening Photographic Dataset.
Mendeley Data.
https://data.mendeley.com/datasets/3xd9n945v8/1
Original Dataset Publication
Xavier P., Rodrigues P.M., Silva C.L.M.
"Shelf-Life Management and Ripening Assessment of Hass Avocado Using Deep Learning Approaches."
Foods, 13(8), 1150.
Contact
For questions, suggestions, or collaboration opportunities, please open an issue in this repository.

---

# Beyond Ripeness Classification: A Survival Analysis Framework for Hass Avocado Shelf-Life Modelling

![Research](https://img.shields.io/badge/Research-Survival%20Analysis-green)
![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Reproducibility](https://img.shields.io/badge/Reproducibility-Enabled-orange)
![Dataset](https://img.shields.io/badge/Dataset-Mendeley%20Data-lightgrey)

---

## Overview

This repository provides the computational workflow accompanying a secondary analysis of the **Hass Avocado Ripening Photographic Dataset** using survival-analysis methods.

The original dataset was developed for image-based ripeness assessment, with repeated photographs of individual Hass avocados classified into predefined ripening stages. In this work, the longitudinal structure of those observations is reformulated as a time-to-event problem.

Rather than asking:

> "What ripening stage is the fruit currently in?"

this study addresses the complementary question:

> "How long do fruits remain before reaching a defined shelf-life endpoint?"

The workflow reconstructs fruit-level longitudinal ripening trajectories and applies Kaplan-Meier estimation, log-rank testing, accelerated failure time modelling, interval-censored survival analysis, restricted mean survival time estimation, and sensitivity analyses.

The primary inferential model is an **interval-censored Weibull accelerated failure time (AFT) model**, which accounts for uncertainty in the exact transition time between daily observations.

This repository is intended to support transparent and reproducible analysis of longitudinal agricultural imaging datasets using time-to-event methods.

---

## Research Question

Can longitudinal fruit-image observations be transformed into a reproducible time-to-event framework for modelling Hass avocado shelf-life progression?

---

## Key Idea

Most image-based ripening studies focus on identifying the current maturity state of a fruit:

> "What ripening stage is the fruit currently in?"

This repository explores a complementary longitudinal question:

> "How long does a fruit remain before reaching the defined shelf-life endpoint?"

The analytical transformation is:

```text
Image-level ripening observations
        ↓
Fruit-level longitudinal trajectories
        ↓
Time-to-event survival dataset
        ↓
Survival modelling and uncertainty estimation
        ↓
Shelf-life progression estimates
```

This framework demonstrates how an existing longitudinal agricultural imaging dataset can be used not only for ripeness classification, but also for statistical modelling of ripening time and shelf-life dynamics.

<img width="1122" height="1402" alt="Avocado Survival Analysis Workflow" src="https://github.com/user-attachments/assets/21568841-8876-40c8-acdc-5de7cb25ba70" />

---

# Dataset

## Hass Avocado Ripening Photographic Dataset

This study uses the publicly available **Hass Avocado Ripening Photographic Dataset** from Mendeley Data.

Dataset source:

https://data.mendeley.com/datasets/3xd9n945v8/1

The dataset contains repeated longitudinal observations of individual Hass avocados during postharvest ripening.

### Dataset Characteristics

- **478 Hass avocado fruits**
- **14,722 image-level records in the distributed spreadsheet**
- **7,361 fruit-day observations**
- **Two photographs per fruit-day**
- **No exact duplicate rows**
- **Three storage conditions**
  - T10: 10 °C
  - T20: 20 °C
  - Ambient temperature
- **Daily longitudinal observations during ripening**
- **Five predefined ripening stages**

The Mendeley Data metadata reports **14,710 photographs**, whereas the distributed spreadsheet analysed in this repository contains **14,722 image-level records**.

The distributed spreadsheet was internally consistent: it contained **7,361 fruit-days with exactly two photographs per fruit-day**, and no exact duplicate rows were detected. Therefore, all 14,722 spreadsheet records were retained for analysis.

---

## Original Dataset Purpose

The original dataset was developed primarily for image-based avocado ripeness assessment, with individual photographs assigned to predefined ripening stages for classification and deep-learning applications.

## Use in This Repository

Rather than treating ripening stages only as categorical image labels, this repository uses the longitudinal structure of the dataset to reconstruct fruit-level ripening trajectories.

Repeated observations are transformed into a time-to-event framework in which progression to a predefined shelf-life endpoint is analysed using survival methods.

This is a **secondary analysis of an existing public dataset**. No new avocado storage experiment was conducted as part of this work.

---

# Methodology

## 1. Fruit-Level Longitudinal Reconstruction

Image-level observations were transformed into daily fruit-level longitudinal trajectories.

Each fruit-day was uniquely identified using:

`Storage condition + Fruit ID + Observation day`

Each avocado was photographed from two opposite sides.

Agreement between the two side-specific ripening classifications was checked before constructing the fruit-day dataset.

Across all **7,361 fruit-days, no disagreement was observed between the two side ratings**.

Therefore, the choice of side-aggregation rule had no effect on the derived daily ripening state.

The maximum ripening stage was retained programmatically when constructing the fruit-day dataset, but minimum, maximum, and mean ratings would be identical because the two side ratings agreed for every fruit-day.

---

## 2. Shelf-Life Endpoint Definition

### Primary Endpoint: Stage 4

Stage 4 was selected as the primary shelf-life endpoint because the original dataset defines Stage 4 as the end of shelf life. Stage 5 represents the overripe stage and was therefore analysed separately as a secondary endpoint.

The survival outcome was defined as:

**Time:** Days after storage initiation  
**Event:** First observation at Stage 4 or higher

For fruits that reached the endpoint, the first day with a ripening classification of Stage 4 or higher was recorded as the observed event day.

Fruits for which Stage 4 was not observed before their final available record were treated as right-censored at their last observed day.

### Stage 4 Analysis Population

| Storage Condition | Fruits | Stage-4 Events | Right-Censored |
|---|---:|---:|---:|
| T10 | 192 | 166 | 26 |
| T20 | 143 | 130 | 13 |
| Ambient | 143 | 130 | 13 |
| **Total** | **478** | **426** | **52** |

This formulation allows incomplete ripening trajectories to contribute information without assuming that the unobserved Stage-4 transition time is known.

---

# Statistical Framework

## Kaplan-Meier Survival Estimation

Kaplan-Meier estimation was used to describe the probability that fruits remained below the Stage-4 endpoint over time.

Group differences were evaluated using:

- Global log-rank testing
- Pairwise T20 versus ambient log-rank testing

The Kaplan-Meier analysis provides a non-parametric description of ripening progression while properly accounting for right-censored observations.

---

## Primary Interval-Censored Weibull AFT Model

Because ripening stage was assessed only once per observation day, the exact time at which a fruit transitioned to Stage 4 was not directly observed.

For example:

```text
Day 12 → Stage 3
Day 13 → Stage 4
```

The actual transition occurred within:

```text
12 < T ≤ 13
```

Therefore, an **interval-censored Weibull accelerated failure time model** was used as the primary effect-estimation model.

For fruits that reached Stage 4, the event interval was defined by the last observation before the endpoint and the first observation at Stage 4 or higher.

Fruits that never reached Stage 4 were represented as right-censored at their final observed day.

T10 was used as the reference storage group.

The model was fitted using the `survival` package in R through `rpy2`.

---

## Standard Weibull AFT Model

A conventional right-censored Weibull AFT model was fitted as a supporting analysis.

This model provides directly interpretable time ratios and was used to assess consistency with the primary interval-censored analysis.

---

## Distributional Sensitivity

The conventional right-censored AFT analysis was repeated using Weibull, log-normal, and log-logistic distributions.

| Model | AIC | T20 vs T10 TR | Ambient vs T10 TR |
|---|---:|---:|---:|
| Weibull | 1559.51 | 0.430 | 0.421 |
| Log-logistic | 1656.67 | 0.413 | 0.411 |
| Log-normal | 1692.90 | 0.420 | 0.415 |

The Weibull model had the lowest AIC.

The estimated time ratios were nevertheless similar across the three parametric distributions, indicating that the main storage-group pattern was not highly sensitive to the selected right-censored AFT distribution.

---

## Restricted Mean Survival Time

Restricted Mean Survival Time (RMST) was calculated as an absolute summary of time remaining below the Stage-4 endpoint.

The analysis used a restriction horizon of **20 days**.

Bootstrap resampling with **1,000 replicates** was used to estimate confidence intervals for:

- Group-specific RMST
- Pairwise RMST differences

---

## Cox Proportional Hazards Model

A Cox proportional hazards model was also fitted as a supplementary analysis.

Because storage groups showed very strong separation in Stage-4 timing and the resulting hazard-ratio estimates were extremely large, Cox hazard ratios are not emphasized as headline results.

The AFT framework was preferred for primary effect interpretation.

---

# Robustness and Sensitivity Analysis

The workflow includes the following robustness checks.

### Duplicate and Fruit-Day Consistency Verification

- No exact duplicate rows were detected.
- All **7,361 fruit-days** contained exactly two photographic observations.

### Fruit-Side Agreement Check

- The two photographed sides had identical ripening-stage classifications for all **7,361 fruit-days**.
- Therefore, side aggregation did not influence the derived survival outcome.

### Censoring Inspection

- **52 fruits** were right-censored in the primary Stage-4 analysis.
- The final observed day and ripening stage were inspected for each censored fruit.

### Short Follow-Up Censoring Sensitivity

- **15 fruits** were censored after only Day 1.
- These observations were temporarily excluded in a sensitivity analysis.
- The overall storage-group median pattern remained unchanged.

### Extreme Censoring Sensitivity Scenarios

Two deliberately extreme scenarios were examined:

- Latest-follow-up scenario
- Earliest-event scenario

The resulting Weibull AFT time ratios remained broadly consistent with the main analysis.

### Alternative Endpoint Analysis

Stage 5 was analysed as a secondary endpoint to determine whether the overall storage-group pattern depended on the selected ripening endpoint.

### Conceptual DAG

A conceptual directed acyclic graph was used to illustrate possible relationships among:

- Baseline fruit characteristics
- Storage chamber context
- Storage condition
- Ripening progression
- Shelf-life endpoint

The DAG is conceptual and is not used to claim causal identification.

---

# Main Results

## Kaplan-Meier Median Time to Stage 4

Kaplan-Meier estimates showed substantial separation between T10 and the warmer storage conditions.

| Storage Condition | KM Median Time to Stage 4 | 95% CI |
|---|---:|---:|
| T10 (10 °C) | 17 days | 17–17 |
| T20 (20 °C) | 7 days | 7–7 |
| Ambient | 7 days | 7–7 |

The global log-rank test showed strong evidence that the survival trajectories differed among storage groups:

**χ² = 407.58, df = 2, p = 3.12 × 10⁻⁸⁹.**

The integer-valued median confidence limits reflect the discrete daily observation schedule and concentration of Stage-4 events at particular observation days.

T10 fruits remained below Stage 4 substantially longer than fruits in either warmer-storage group.

---

<img width="2539" height="1638" alt="kaplan_meier_curve" src="https://github.com/user-attachments/assets/24e0627c-f8af-464a-b14e-c4041ac43483" />

**Kaplan-Meier survival curves showing delayed progression toward Stage 4 under T10 storage compared with T20 and ambient storage.**

---

# Primary Interval-Censored Weibull AFT Results

The primary interval-censored Weibull AFT model showed substantially shorter time to Stage 4 under T20 and ambient storage relative to T10.

| Comparison | Time Ratio | 95% CI |
|---|---:|---:|
| T20 vs T10 | 0.411 | 0.398–0.425 |
| Ambient vs T10 | 0.403 | 0.390–0.416 |

Relative to T10, the estimated time to Stage 4 was approximately **41% as long under T20** and **40% as long under ambient storage**.

The supporting conventional Weibull AFT model produced similar estimates:

- **T20 vs T10: TR = 0.430**
- **Ambient vs T10: TR = 0.421**

The consistency between the standard and interval-censored analyses supports the robustness of the estimated storage-group associations.

These estimates describe **associations between storage condition and observed ripening timing** and should not be interpreted as definitive causal treatment effects.

---

## T20 vs Ambient Comparison for Stage 4

The pairwise Stage-4 log-rank comparison between T20 and ambient storage showed no detectable difference:

**χ² = 1.02, p = 0.314.**

Thus, for the **primary Stage-4 endpoint**, T20 and ambient storage showed similar survival trajectories.

This result should not be interpreted as proof that the two storage conditions are statistically equivalent.

---

# Restricted Mean Survival Time Results

RMST was calculated using a restriction horizon of **20 days**.

| Storage Condition | RMST (days) | Bootstrap 95% CI |
|---|---:|---:|
| T10 | 16.17 | 15.82–16.54 |
| T20 | 6.80 | 6.59–6.99 |
| Ambient | 6.71 | 6.52–6.90 |

Pairwise RMST differences were:

| Comparison | RMST Difference (days) | 95% CI |
|---|---:|---:|
| T10 − T20 | 9.37 | 8.93–9.78 |
| T10 − Ambient | 9.45 | 9.03–9.85 |
| T20 − Ambient | 0.08 | −0.19–0.36 |

RMST results were consistent with the Kaplan-Meier and AFT analyses.

T10 had substantially greater restricted mean time remaining below Stage 4 than either warmer-storage group.

The Stage-4 RMST difference between T20 and ambient was small and its confidence interval included zero.

---

<img width="2100" height="1407" alt="shelf_life_distribution" src="https://github.com/user-attachments/assets/01be1b6c-bd1e-4602-81b7-65ae8b9b18f0" />

*Distribution of observed event or censoring times by storage condition. Because censored observations do not represent known Stage-4 event times, this figure is descriptive only; survival-model estimates are the primary basis for inference.*

---

# Sensitivity Results

## Fruit-Side Agreement

The two photographed sides had identical ripening-stage classifications for all **7,361 fruit-days**.

- Fruit-days with different side ratings: **0**
- Total fruit-days: **7,361**

Therefore, the choice of side-aggregation rule had no effect on the derived survival outcomes.

---

## Short Follow-Up Censoring Sensitivity

Among the 52 right-censored fruits, **15 were censored after only Day 1**.

After temporarily excluding these short-follow-up observations, the median pattern remained:

- T10: **17 days**
- T20: **7 days**
- Ambient: **7 days**

Thus, the principal storage-group pattern was not driven by the Day-1-only censored observations.

---

## Extreme Censoring Scenario Sensitivity

Two additional hypothetical censoring scenarios were examined.

| Scenario | T20 vs T10 TR | Ambient vs T10 TR |
|---|---:|---:|
| Latest-follow-up scenario | 0.404 | 0.399 |
| Earliest-event scenario | 0.431 | 0.423 |

The estimated time ratios remained broadly consistent across the scenarios.

These scenarios are sensitivity analyses rather than formal statistical bounds and cannot determine the true censoring mechanism.

---

## Stage 5 Endpoint Sensitivity

Stage 5 was analysed as a secondary endpoint.

| Storage Condition | KM Median Time to Stage 5 |
|---|---:|
| T10 | 22 days |
| T20 | 10 days |
| Ambient | 9 days |

The supporting Weibull AFT time ratios relative to T10 were:

- **T20: TR = 0.431**
- **Ambient: TR = 0.410**

Unlike the primary Stage-4 endpoint, T20 and ambient storage showed a detectable difference for Stage 5:

**T20 vs Ambient log-rank: χ² = 57.72, p = 3.02 × 10⁻¹⁴.**

Thus, the broad conclusion that T10 had the longest ripening trajectory was robust to endpoint choice.

However, the similarity between T20 and ambient observed for the primary Stage-4 endpoint did **not** persist for Stage 5.

---

<img width="2539" height="1638" alt="KM_stage5_sensitivity" src="https://github.com/user-attachments/assets/57b42ef6-407f-41fc-b601-6d785f41daea" />

**Kaplan-Meier sensitivity analysis using Stage 5 as an alternative endpoint. T10 retained the longest ripening trajectory, while T20 and ambient became distinguishable at this later endpoint.**

---

# Interpretation

Taken together, the Kaplan-Meier, RMST, conventional Weibull AFT, and primary interval-censored Weibull AFT analyses support the following conclusions:

- **T10 storage was associated with substantially longer time to the primary Stage-4 endpoint.**
- **For Stage 4, T20 and ambient storage showed no detectable difference in the pairwise log-rank comparison (p = 0.314).**
- **The primary interval-censored AFT estimates were consistent with the supporting conventional Weibull AFT analysis.**
- **The main T10-versus-warmer-storage pattern remained stable across censoring sensitivity analyses.**
- **Stage 5 sensitivity analysis preserved the longer T10 trajectory but revealed a detectable difference between T20 and ambient.**

Because this study is a secondary analysis of an existing dataset, storage-group comparisons are interpreted as **associations between storage regime and observed ripening progression**, rather than as definitive causal effects of temperature.

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
│   ├── KM_median_survival_with_CI.csv
│   ├── logrank_results.csv
│   ├── logrank_T20_vs_Tam.csv
│   ├── aft_results.csv
│   ├── AFT_distribution_sensitivity.csv
│   ├── AFT_standard_vs_interval_comparison.csv
│   ├── interval_censored_weibull_AFT_results.csv
│   ├── interval_censored_weibull_AFT_results.txt
│   ├── RMST_results.csv
│   ├── RMST_with_bootstrap_CI.csv
│   ├── RMST_pairwise_differences_with_CI.csv
│   ├── stage5_T20_vs_Tam_logrank.csv
│   ├── stage5_weibull_AFT_results.csv
│   └── final_analysis_summary_revised.csv
│
├── requirements.txt
│
└── LICENSE
```

> The repository tree above should reflect files actually committed to the repository. Output files generated by the notebook can be added to the `results/` directory as appropriate.

---

# Reproducibility

This repository is designed to provide a transparent and reproducible implementation of the survival-analysis workflow.

## Installation

Clone the repository:

```bash
git clone https://github.com/SumaiyaData/avocado-survival-analysis.git
cd avocado-survival-analysis
```

Install the required Python dependencies:

```bash
pip install -r requirements.txt
```

The primary interval-censored model additionally requires **R** and the R `survival` package. Python communicates with R through `rpy2`.

The notebook contains the complete analytical workflow, including:

- dataset preparation
- duplicate and fruit-day consistency checks
- fruit-level trajectory reconstruction
- Stage-4 survival-dataset generation
- censoring inspection
- Kaplan-Meier analysis
- log-rank testing
- conventional Weibull AFT modelling
- AFT distributional sensitivity analysis
- primary interval-censored Weibull AFT modelling
- RMST estimation
- bootstrap confidence intervals
- censoring sensitivity analyses
- Stage-5 endpoint sensitivity analysis
- conceptual DAG generation

### Software Environment Used for the Final Analysis

- Python: **3.13.16**
- lifelines: **0.30.3**
- pandas: **2.2.3**
- NumPy: **2.1.3**
- rpy2: **3.5.17**
- R: **4.6.1**
- R `survival`: **3.8-12**

---

# Data Availability

The original Hass avocado image dataset is **not redistributed in this repository**.

It can be downloaded from the official Mendeley Data record:

https://data.mendeley.com/datasets/3xd9n945v8/1

The analysis notebook expects the original dataset files to be obtained from this source.

---

# Limitations

This repository represents a secondary analysis of an existing public dataset.

Important limitations include:

- No new avocado storage experiment was conducted for this analysis.
- The storage-allocation mechanism was not fully documented in the source materials.
- The available source information did not clearly establish the number of independent storage chambers used for each condition.
- Therefore, storage-group comparisons are interpreted as **associations**, rather than definitive causal temperature effects.
- For 52 fruits, Stage 4 was not observed before the final available record. The reason follow-up ended for these fruits was not documented in the available dataset.
- Sensitivity analyses were used to evaluate the influence of censoring assumptions, but they cannot establish that censoring was non-informative.
- Biological quality measurements such as firmness, chemical composition, and sensory quality were not available for inclusion in the survival models.
- The survival endpoints are based on the ripening-stage classifications supplied in the original dataset.
- The analysis demonstrates a statistical framework for reusing longitudinal image datasets and is not a newly conducted storage trial.

---

# Relationship to the Original Dataset Study

The original Hass Avocado Ripening Photographic Dataset was developed primarily for image-based ripeness assessment and classification.

This repository extends the analytical use of that dataset by treating longitudinal ripening progression as a time-to-event problem.

The objective is not to replace image-classification approaches, but to demonstrate an additional statistical framework for modelling shelf-life dynamics from repeated agricultural imaging observations.

---

# Citation

If you use this workflow, please cite the repository, the original dataset, and the associated publication.

## Repository

**Beyond Ripeness Classification: A Survival Analysis Framework for Hass Avocado Shelf-Life Modelling**

GitHub repository:

https://github.com/SumaiyaData/avocado-survival-analysis

## Dataset

Xavier, Pedro; Rodrigues, Pedro; Silva, Cristina L. M. (2024).

**Hass Avocado Ripening Photographic Dataset.**

Mendeley Data, Version 1.

https://doi.org/10.17632/3xd9n945v8.1

## Related Publication

Xavier, P., Rodrigues, P. M., & Silva, C. L. M. (2024).

**Shelf-Life Management and Ripening Assessment of Hass Avocado Using Deep Learning Approaches.**

*Foods, 13*(8), 1150.

---

## License

See the `LICENSE` file in this repository for the terms governing reuse of the repository code.

The original dataset remains subject to the terms specified by the original data provider.

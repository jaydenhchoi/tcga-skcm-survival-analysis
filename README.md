# tcga-skcm-survival-analysis
---
## Overview & Scientific Rationale
This bioinformatics project evaluates the prognostic value of selected biomarkers (`CD8A`, `CD274`/PD-L1) alongside oncogenic mutations (`BRAF`) in cutaneous melanoma using the TCGA-SKCM cohort data from the National Cancer Institute GDC Data portal.

This pipeline combines biomarker data with clinical survival outcomes to investigate how they interact in predicting overall survival against cutaneous melanoma.

---

## Project Structure
This project was undertaken in three distinct phases:

* **Phase 1:** Clinical Cohort Construction & Data Cleaning

In Phase 1, the clinical registry for the TCGA-SKCM cohort ($n = 470$) was retrieved from the GDC Data Portal and ingested into a Pandas dataframe for processing. Text placeholders were replaced with null values, and duplicate patient entries were resolved to construct a clean dataset. Follow-up timelines were converted into a survival_months duration and vital status was binarised (1 = Dead, 0 = Alive). Finally, patient records missing survival outcomes were removed from the dataset for subsequent analysis.

* **Phase 2:** Univariate Stratified Survival Modeling

In Phase 2, BRAF mutation data was extracted from the TCGA-SKCM Cohort MAF data on the GDC Portal and ingested into Pandas. Patient barcodes were parsed to deduplicate entries and map binary mutation calls (MUT vs. WT) to corresponding patient IDs in the clinical cohort. To measure the prognostic impact of BRAF mutations, Kaplan-Meier survival curves were generated for both groups, and a formal log-rank test was conducted to evaluate differences in survival distributions.

* **Phase 3:** Multivariate Cox Proportional Hazards Regression Model

In Phase 3, RNA-seq expression data for CD8A and CD274 (PD-L1) were extracted from the TCGA-SKCM dataset on cBioPortal and ingested into Pandas. The data was deduplicated, $\log_2(x+1)$ transformed, and merged with the main clinical dataframe. Median imputation was applied to resolve missing expression values. A multivariate Cox Proportional Hazards model was then created to measure the independent prognostic value of CD8A, CD274, and BRAF mutation status.

---

## Key Findings
* **CD8A Expression:** Higher `CD8A` expression is significantly associated with improved survival ($\text{HR} = 0.89$), displaying an approximate 11% reduction in mortality hazard.
* **CD274 (PD-L1) Expression:** High `CD274` expression correlates with stronger survival, consistent with the expected adaptive immune response against inflamed tumors.
* **BRAF Status:** While an initial Kaplan-Meier curve plot suggested baseline survival differences for `BRAF`-mutated tumors, `BRAF` status lost independent prognostic significance ($\text{HR} = 0.84, p = 0.21$) in Cox Proportional Hazard Regression modeling when adjusting for immune biomarkers (`CD8A`, `CD274`).

---

## Key Visualizations

| Kaplan-Meier Survival Analysis | Multivariate Cox Proportional Hazards Model |
| :---: | :---: |
| ![Kaplan-Meier Plot](figures/km_braf_survival.png) | ![Cox Forest Plot](figures/cox_forest_plot.png) |

---

## Repository Structure

```plaintext
tcga-skcm-survival-analysis/
│
├── README.md
├── requirements.txt
│
├── data-pipeline/
│   ├── clinical.tsv
│   ├── mutations.maf
│   └── skcm_survival.ipynb   <-- script used for data analysis
│
└── figures/
    ├── km_braf_survival.png
    └── cox_forest_plot.png

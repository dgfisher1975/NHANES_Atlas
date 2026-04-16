# Biomarker-Measured Exposure–Phenome Atlas
# Stratified by Self-Identified Race/Ethnicity
# Analytic Code Package (Manuscript v1.0)
# =============================================================

## Manuscript

Race/ethnicity-stratified exposome–phenome atlas based on NHANES 1999–2018.
Links 71 exposure biomarkers with 48 clinical phenotypes across five
racial/ethnic groups and a pooled sample. Primary analytic sample:
**55,081 adults aged ≥20 years** (NHW=24,304; NHB=11,516; MA=9,593;
OH=4,522; OTH=5,146).

Methodology adapted from: Patel CJ, Ioannidis JPA, Manrai AK. An atlas
of exposome–phenome associations in health and disease risk. Nat Med
2026. DOI: 10.1038/s41591-026-04266-0

## Repository

https://github.com/dgfisher1975/NHANES_Atlas  (MIT licence)

## Prerequisites

- R ≥ 4.5.1
- NHANES 1999–2018 XPT files at `C:/NHANES/NHANES/`
  (modify `NHANES_DATA_DIR` in `01_phase1_data_prep/00_config.R`)
- Disk: ~50 GB (raw XPT + cache)
- Runtime: Phase 1 ~30 min; Phase 2 ~5–8 h

## Directory Structure

```
NHANES_R/
├── 01_phase1_data_prep/      # Data ingestion, variable cataloguing
├── 02_phase2_core_analysis/  # Main ExWAS regressions (two dose scales)
├── 03_sensitivity_analyses/  # Sensitivity packages → Tables S1, S4, S6, S7
├── 04_figures/               # Figure 5 & 6 generation
└── 05_supplementary_tables/  # Demographics (S5) & Table 3 adult n extraction
```

## Execution

```r
setwd("E:/NHANES_R")          # your local root

# ─── PHASE 1: Data preparation (~30 min) ───────────────────
source("01_phase1_data_prep/run_phase1.R")
#   runs: 00_config.R → 01_data_loading.R
#         → 02_stratification_sample_size.R → 03_data_merge.R

source("01_phase1_data_prep/01b_build_variable_index.R")
#   scans 1,609 XPT column headers to build variable→file index

# ─── PHASE 2: Core analysis (~3 h) ────────────────────────
source("02_phase2_core_analysis/phase2_v3.R")
#   Within-group z-score ExWAS; 20,448 survey-weighted regressions
#   → Table 1, Table 2, Figures 2-5 data

source("02_phase2_core_analysis/phase2_common_scale.R")
#   Common dose scale (log₁₀, unstandardised)
#   → Table 3 data, interaction results for Figures 6-7

# ─── PHASE 3: Sensitivity analyses (~2 h) ─────────────────
source("03_sensitivity_analyses/phase2_adult_sensitivity.R")
#   All-ages comparison → Supplementary Table S1

source("03_sensitivity_analyses/phase2_extra_confounder_top10.R")
#   +BMI +cotinine adjustment → Supplementary Table S6

source("03_sensitivity_analyses/phase2_supplement_final_v2.R")
#   Below-LOD proportions, 7-item sensitivity summary
#   → Supplementary Tables S2, S4

source("03_sensitivity_analyses/phase2_robustness_package.R")
#   Log-outcome and additional robustness
#   → Supplementary Table S7 data

# ─── PHASE 4: Figures & supplementary tables (~15 min) ────
source("04_figures/fig5_interaction_volcano_final.R")
#   → Figure 6 (interaction volcano, common scale, 244/3,127 FDR)

source("04_figures/fig6_forest_final_v2.R")
#   → Figure 7 (forest plot, P-ranked top 10 interactions)

source("05_supplementary_tables/phase2_demographics_table.R")
#   → Supplementary Table S5 (≥20 yr baseline demographics)

source("05_supplementary_tables/table3_extract_adult_n.R")
#   → Adult-restricted n values for Table 3
```

## Script → Manuscript Output Map

### Main Text

| Item | Source Script | Description |
|------|---------------|-------------|
| Figure 1 | (BioRender) | Study design schematic |
| Figure 2 | phase2_v3.R | P-value distribution |
| Figure 3 | phase2_v3.R | Bonferroni-significant counts |
| Figure 4 | phase2_v3.R | Cross-race β scatter |
| Figure 5 | phase2_v3.R | R² distribution |
| Figure 6 | fig5_interaction_volcano_final.R | Interaction volcano (244/3,127) |
| Figure 7 | fig6_forest_final_v2.R | Forest plot (top 10 P-ranked) |
| Table 1 | phase2_v3.R | ExWAS summary by race |
| Table 2 | phase2_v3.R | Pairwise β correlations |
| Table 3 | phase2_common_scale.R + table3_extract_adult_n.R | Top 10 interactions with adult-restricted n |

### Supplementary

| Item | Source Script | Description |
|------|---------------|-------------|
| Table S1 | phase2_adult_sensitivity.R | Primary (≥20 yr) vs all-ages specification |
| Table S2 | phase2_supplement_final_v2.R | Below-LOD proportions |
| Table S3 | (static) | Subsample weight assignment |
| Table S4 | phase2_supplement_final_v2.R | 11-item sensitivity summary |
| Table S5 | phase2_demographics_table.R | ≥20 yr baseline demographics |
| Table S6 | phase2_extra_confounder_top10.R | +BMI +cotinine detail |
| Table S7 | phase2_robustness_package.R | Log-outcome detail |

## Two Exposure Dose Scales

| Scale | Used for | β interpretation |
|-------|----------|------------------|
| Within-group z-score | Tables 1, 2; Figures 2-5 | Per 1-SD increase, within-group |
| Common dose (log₁₀, unstandardised) | Table 3; Figures 6-7; all sensitivity | Per 10-fold exposure increase |

## Key Parameters (`01_phase1_data_prep/00_config.R`)

| Parameter | Default | Description |
|-----------|---------|-------------|
| `NHANES_DATA_DIR` | `C:/NHANES/NHANES` | NHANES XPT root |
| `PROJECT_DIR` | `E:/NHANES_DATA/ExWAS_Race_Stratified` | Output root |
| `MIN_SAMPLE_SIZE` | 300 | Min n per race–exposure–phenotype cell |
| `MIN_SURVEY_WAVES` | 2 | Min survey cycles required |
| `FDR_LEVEL` | 0.05 | Benjamini–Yekutieli threshold |

## Software

R ≥ 4.5.1 with: survey, dplyr, tidyr, purrr, stringr, haven, data.table,
broom, ggplot2, patchwork, scales, logger, pwr

## Data Availability

NHANES public-use data: https://www.cdc.gov/nchs/nhanes/
Summary statistics database accompanying this manuscript: see Data
Availability statement in the main text.

## Citation

[To be added upon acceptance]

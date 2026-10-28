# Forecasting 7-Day ICU Bed Demand via Patient Deterioration Modeling
IE7275 Group A project — *I See You, ICU*.
Authors: Ka Won Cho, Minh Tuong Le, Shanti Ram Pokhrel.

## 1. Problem Statement & Intended Users

### Problem Statement
Intensive Care Units (ICUs) face chronic bed capacity and staffing bottlenecks, particularly in healthcare systems strained by demographic aging. Unplanned transfers from general hospital wards into ICUs create severe, unpredictable surges in critical care demand. Conventional capacity planning relies heavily on historical rolling averages (e.g., historical bed utilization), which completely ignore the real-time physiological deterioration of currently admitted inpatients. Conversely, bedside early warning systems (e.g., NEWS2) offer very short lead times (4–12 hours), which is insufficient for forward operational planning.

This project implements a hierarchical predictive framework that combines micro-level clinical prediction with macro-level capacity forecasting:
1. **Micro Model (Patient Level):** Predicts the 24-hour and 48-hour probabilities of unplanned ICU transfer for high-risk, frequently admitted inpatients currently on general floors 
2. **Macro Aggregator (Ward Level):** Aggregates individual calibrated transfer risks across a closed cohort of inpatients to project the expected total 7-day ICU bed demand with exact analytical 95% confidence intervals using a Poisson-Binomial distribution model.

### Intended Users
- **Primary Users (Hospital Operation managers & Bed Coordinators):** Utilize the 7-day aggregate bed demand forecaster to proactively rebalance critical care nursing rosters, allocate respiratory equipment, and adjust elective post-operative surgical schedules before bottlenecks occur.
- **Secondary Users (Rapid Response Teams & Ward Clinicians):** Utilize the patient-level risk watchlist to identify rapidly decompensating frequent admitters up to 24 hours prior to acute failure for timely bedside intervention.

---

## 2. Data Sources & Licensing

### Source Dataset
- **Database:** Medical Information Mart for Intensive Care IV (**MIMIC-IV v3.1**)
- **URL:** [https://physionet.org/content/mimiciv/3.1/](https://physionet.org/content/mimiciv/3.1/)
- **Provider:** PhysioNet / Massachusetts Institute of Technology (MIT) Computational Physiology Lab.
- **Download Date:** 2026-09-23
- **Institutional Origin:** Beth Israel Deaconess Medical Center (BIDMC), Boston, MA.

### Dataset Access & Acquisition
- **License:** [PhysioNet Credentialed Health Data License 1.5.0](https://physionet.org/content/mimiciv/view-license/3.1/) |
                under PhysioNet Restricted Data Use Agreement (DUA) / Creative Commons Attribution 4.0 International (CC BY 4.0).
    **The data are not included in this repository and must not be redistributed.** 
    The license above applies to the data only; the MIT license in [`LICENSE`](LICENSE) covers the code.
- **Access Requirements:** Access requires credentialing on PhysioNet, completion of the CITI "Data or Specimens Only Research" training, and signing the DUA.
- **Data Modules Used:**
  - `hosp.patients`, `hosp.admissions`, `hosp.transfers`, `hosp.labevents`, `hosp.prescriptions`, `hosp.diagnoses_icd`, `hosp.omr`, `hosp.services`
  - `icu.icustays`

### How to obtain the data
1. Create a PhysioNet account and complete credentialing.
2. Complete the CITI course *"Data or Specimens Only Research"* and upload the certificate.
3. Sign the data use agreement on the MIMIC-IV 3.1 project page.
4. Download the files and place them, unchanged (still gzipped), under `data/raw/`:

---

## 3. Installation

Requires **Python 3.12** (`pyproject.toml` pins `>=3.12,<3.13`). About 16 GB of RAM is
recommended; `labevents` (2.6 GB compressed) is streamed in blocks.

```bash
git clone https://github.com/wallrue/Forecasting-Unplanned-Ward-to-ICU-Transfer.git
cd Forecasting-Unplanned-Ward-to-ICU-Transfer

python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

pip install --upgrade pip
pip install -r requirements.txt    # pinned versions; also installs this package (-e .)
pip install jupyter seaborn        # only needed for the notebooks
```

### Key Dependencies (`requirements.txt`)
```text
google-cloud-bigquery>=3.10.0
pandas>=2.0.0
numpy>=1.24.0
scikit-learn>=1.3.0
lightgbm>=4.0.0
xgboost>=1.7.0
scipy>=1.10.0
matplotlib>=3.7.0
seaborn>=0.12.0
streamlit>=1.25.0
```

---

## 4. Reproducing the Results

To reproduce all results and figures reported in the project study, follow the pipeline steps sequentially:

### Step 1: Data Access
Download dataset from Physionet site:

### Step 2: Clean Data and Extract Feature Matrix & Cohorts

```bash
python -m icu_forecast.clean
python -m icu_forecast.split
python -m icu_forecast.features
python -m icu_forecast.prepare
python -m icu_forecast.export_csv
jupyter nbconvert --to notebook --execute --inplace notebooks/01_eda.ipynb
jupyter nbconvert --to notebook --execute --inplace notebooks/02_pca.ipynb
```

## 5. Repository structure

```
.
├── README.md                 # this file
├── LICENSE                   # MIT license for the code
├── config.yaml               # all paths, seed, split ratios, cohort and feature settings
├── pyproject.toml            # package metadata (installs src/icu_forecast)
├── requirements.txt          # pinned dependencies
│
├── src/icu_forecast/         # pipeline package (run with python -m icu_forecast.<module>)
│   ├── config.py             # loads config.yaml, resolves paths from the repo root
│   ├── clean.py              # cleans raw MIMIC-IV tables, builds the cohort
│   ├── split.py              # patient-level stratified train/val/test split
│   ├── features.py           # 08:00 snapshots, 24 h / 48 h labels, 137 features
│   ├── prepare.py            # imputation, scaling, encoding fitted on train only
│   └── export_csv.py         # exports processed parquet files to CSV
├── code/                     # earlier copy of the pipeline scripts (superseded by src/)
│
├── notebooks/
│   ├── 01_eda.ipynb          # exploratory data analysis
│   └── 02_pca.ipynb          # PCA on the routine lab panel
├── sql/
│   └── 01_pipeline_validation.sql   # SQL cross-check of the cohort definition
│
├── reports/
│   ├── IE7275_GroupA_Project_Proposal_M3.pdf   # milestone report (.doc source alongside)
│   ├── data_pipeline.md      # before/after counts for every pipeline step
│   └── figures/              # figures used in the reports
│
└── data/                     # not tracked by Git; created locally
    ├── raw/                  # original MIMIC-IV files (read-only)
    ├── processed/            # parquet outputs of the pipeline
    └── processed_csv/        # CSV export (step 5)
```

## 6. License

- **Code:** MIT License — see [`LICENSE`](LICENSE).
- **Data:** MIMIC-IV v3.1 is distributed by PhysioNet under the PhysioNet Credentialed Health
  Data License 1.5.0. No patient-level data is stored in this repository.

## 7. Citation

Johnson, A., Bulgarelli, L., Pollard, T., Gow, B., Moody, B., Horng, S., Celi, L. A., & Mark, R.
(2024). *MIMIC-IV* (version 3.1). PhysioNet. https://doi.org/10.13026/kpb9-mt58

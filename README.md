# Forecasting 7-Day ICU Bed Demand via Patient Deterioration Modeling
This is the repository for IE7275 GroupA project: I see you, ICU

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
- **Institutional Origin:** Beth Israel Deaconess Medical Center (BIDMC), Boston, MA.

### Dataset Access & Acquisition
- **License:** PhysioNet Restricted Data Use Agreement (DUA) / Creative Commons Attribution 4.0 International (CC BY 4.0).
- **Access Requirements:** Access requires credentialing on PhysioNet, completion of the CITI "Data or Specimens Only Research" training, and signing the DUA.
- **Data Modules Used:**
  - `hosp.patients`, `hosp.admissions`, `hosp.transfers`, `hosp.labevents`, `hosp.prescriptions`, `hosp.diagnoses_icd`, `hosp.omr`, `hosp.services`
  - `icu.icustays`

---

## 3. Installation & Dependencies

### Environment Setup
- Python version: **3.10+**
- Recommended virtual environment tool: `conda` or `venv`

### Setup Instructions

```bash
# Clone the repository
git clone https://github.com/your-org/icu-inflow-forecasting.git
cd icu-inflow-forecasting

# Create and activate a Python environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install --upgrade pip
pip install -r requirements.txt
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

### Step 2: Extract Feature Matrix & Cohorts
Run the feature extraction script using the SQL pipeline to query MIMIC-IV v3.1 and output the local feature matrix:


## 5. Repository Structure

ie7275-GroupA/
│
├── README.md              # Project overview, data specs, setup, and reproduction guide
├── LICENSE                # Terms for reusing code
├── requirements.txt       # Pinned project dependencies
├── config.yaml            # Paths, random seeds, and global configuration settings
├── .gitignore             # Excludes raw data, credentials, and virtual environments
│
├── data/                  # Local data storage (ignored by Git)
│   ├── raw/               # Original read-only data extracts
│   └── processed/         # Cleaned and engineered feature datasets
│
├── notebooks/             # Exploratory notebooks (ordered: 01_eda.ipynb, 02_pca.ipynb)
│   ├── 01_eda.ipynb       # Exploratory Data Analysis
│
├── sql/                   # SQL repository
│   ├── pipeline_validation.sql      # Data Pipeline Validation
│
├── src/                   # Reusable source code package
│   └── ie7275-GroupA/
│       ├── __init__.py    # Package initializer
│       ├── clean.py       # Data cleaning and error/duplicate handling
│       ├── prepare.py     # Imputation and scaling (fitted on training data only)
│       ├── split.py       # Patient-level data splitting (train/val/test)
│       ├── features.py    # Feature engineering modules
│       └── models/        # Modeling pipelines and evaluation scripts
│
├── reports/figures/       # Generated charts and figures for the final report
├── app/                   # Web-based microproduct dashboard code
└── tests/                 # Unit tests for core functions and validation scripts

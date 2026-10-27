# Forecasting 7-Day ICU Bed Demand via Patient Deterioration Modeling
This is the repository for IE7275 GroupA project: I see you, ICU

## 1. Problem Statement & Intended Users

### Problem Statement
Intensive Care Units (ICUs) face chronic bed capacity and staffing bottlenecks, particularly in healthcare systems strained by demographic aging. Unplanned transfers from general hospital wards into ICUs create severe, unpredictable surges in critical care demand. Conventional capacity planning relies heavily on historical rolling averages (e.g., historical bed utilization), which completely ignore the real-time physiological deterioration of currently admitted inpatients. Conversely, bedside early warning systems (e.g., NEWS2) offer very short lead times (4–12 hours), which is insufficient for forward operational planning.

This project implements a hierarchical predictive framework that combines micro-level clinical prediction with macro-level capacity forecasting:
1. **Micro Model (Patient Level):** Predicts the 24-hour and 48-hour probabilities of unplanned ICU transfer for high-risk, frequently admitted inpatients currently on general floors ($Y_{i} \in \{0, 1\}$).
2. **Macro Aggregator (Ward Level):** Aggregates individual calibrated transfer risks across a closed cohort of inpatients to project the expected total 7-day ICU bed demand ($\mathbb{E}[N_{\text{ICU}}]$) with exact analytical 95% confidence intervals using a Poisson-Binomial distribution model.

### Intended Users
- **Primary Users (Hospital Operations & Bed Capacity Managers):** Utilize the 7-day aggregate bed demand forecaster to proactively rebalance critical care nursing rosters, allocate respiratory equipment, and adjust elective post-operative surgical schedules before bottlenecks occur.
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

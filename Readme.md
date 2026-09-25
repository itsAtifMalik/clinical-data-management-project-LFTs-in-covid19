# COVID-19 Inpatient Liver Injury Biomarker Registry & eCRF Architecture
Project Title: Deranged Liver Enzymes in COVID-19

Link: https://zenodo.org/records/22961182?token=eyJhbGciOiJIUzUxMiJ9.eyJpZCI6ImRiM2RkYzc4LTBiYmUtNGU3NC1iNjMyLWIyZDkwOGFiZDRkMiIsImRhdGEiOnt9LCJyYW5kb20iOiIyZjUxNjE5ZTMxZDZhNDI3Njg2ODg2MjYyOWE2N2UxNCJ9.uwDwd8bjagmsgikW2pv-IamKlZ5qVzaxJ5jrMKqn2i-LqlmkdNuu7bqjntWJjZp32639annIScmTw3ipjhWxiw


A GCP- and 21 CFR Part 11-compliant longitudinal Electronic Data Capture (EDC) system designed in REDCap. This project translates a peer-reviewed inpatient observational study into a structured clinical data management pipeline with dynamic validation rules, automated safety triggers, and audit-ready data workflows.

---

## 1. Clinical Context & Rationale

* **Underlying Study:** Based on the published observational study *"Deranged Liver Enzymes in COVID-19 Patients; and its Impacts on Prognosis"* (Mayo Hospital Lahore).
* **Objective:** Capture baseline patient characteristics, monitor paired biomarker kinetics (Day 1 vs. Day 7), track critical safety events (acute transaminitis and SAEs), and record tertiary clinical outcomes (ICU transfer, discharge, in-hospital mortality).
* **Target Population:** Inpatient adults (17–90 years) admitted with confirmed or suspected COVID-19 infection.

---

## 2. Technical Architecture & Data Management Design

The database was implemented as a **Longitudinal Project with Repeating Instruments**:

### Event Configuration
1. **Event 1: Day 1 (Baseline & Admission):** Demographics, admission date, COVID-19 PCR status, comorbidity checklists, and Day 1 baseline LFTs.
2. **Event 2: Day 7 (Biomarker Kinetics):** Day 7 follow-up laboratory LFTs to evaluate longitudinal hepatic enzyme variation.
3. **Event 3: Study Completion / Disposition:** Primary outcome, documented date of ICU escalation, or documented date of death.

### Core Data Integrity & Regulatory Implementations
* **ICH GCP E6(R3) Safety Escalation:** The Adverse Events form is configured as a repeating instrument across all events. Dynamic branching logic requires completion of SAE seriousness criteria, causal relationship, and expedited sponsor/IRB reporting dates (within 24 hours) whenever an SAE is flagged.
* **Automated Data Quality & Flags:** Implemented calculated logic (`if([alt_level] > 55, 1, 0)`) to dynamically flag acute liver derangement across laboratory entry points without manual intervention.
* **Range Validation (Soft Bounds):** Bounded continuous numerical fields (e.g., ALT `5–1000 U/L`, Bilirubin `0.1–20 mg/dL`, Age `17–90`) to prevent data-entry outliers while presenting reference limits in field notes.
* **Data Privacy (POPIA / HIPAA):** Direct identifiers (`first_name`, `last_name`, `date_of_admission`) are flagged in metadata to enforce automated redaction during de-identified exports.
* **Audit Trail Compliance (21 CFR Part 11):** Configured full system logging to track data changes, user signatures, timestamps, and query resolutions.

---

## 3. Simulated Clinical Case Profiles

The repository contains compiled mock patient profiles demonstrating system verification across diverse clinical trajectories:

* **Study ID 1 (Low-Risk / Recovery):** 33-year-old male with no pre-existing comorbidities and normal Day 1/Day 7 biomarkers; uneventful recovery and hospital discharge.
* **Study ID 2 (Severe Transaminitis / ICU Escalation):** 53-year-old male exhibiting acute Day 7 transaminitis (ALT 310 U/L, Bilirubin 2.4 mg/dL); triggers a life-threatening SAE entry and expedited ICU transfer.
* **Study ID 3 (Multimorbid / Fatal Outcome):** 66-year-old male with DM, HTN, and IHD; exhibits elevated inflammatory and liver markers culminating in acute respiratory failure and documented in-hospital mortality aligned with GCP fatal SAE guidelines.

---

## 4. Repository Contents

* `docs/study_protocol_synopsis.md`: Clinical trial protocol synopsis detailing endpoints, schedule of assessments, and regulatory frameworks.
* `data_architecture/REDCap_metadata_architecture.xml`: Full CDISC ODM XML blueprint enabling instant recreation of the project on any REDCap server.
* `data_architecture/data_dictionary.csv`: Variable codebook containing variable names, field types, validation rules, and branching formulas.
* `mock_exports/Demo_Data_LFTs_in_COVID_19.pdf`: 23-page compiled PDF export of entered mock cases verifying longitudinal form logic.

---

## Disclaimer
All patient names, identification numbers, and dates included in this repository are strictly synthetic/simulated dummy data created solely for system testing, validation, and educational demonstration.

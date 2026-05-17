# Project Lighthouse – Analytical Deliverables

**Candidate:** Vishal Patil
**Role:** Data Analyst (L4)

---

## Submission Overview

Thank you for reviewing my submission for the L4 Data Analyst assessment. Enclosed within this package are the requested deliverables reconciling the HMDA LAR datasets, identifying material outliers, and providing strategic insights to senior leadership.

### Delivery Navigation (Submissions Folder)

1. **`Code & Source Files/HMDA_Reconciliation.ipynb`**

   * This reproducible Jupyter Notebook contains the end-to-end analytics pipeline. It executes the population segmentation (Task 1), automated KPI drift calculation (Task 2), and generates the statistical pattern visualizations natively (Task 3).
2. **`Patil_Vishal_Executive_Summary.pdf`**

   * The Task 4 final deliverable intended for the CFO/Senior Leadership. This file summarizes findings, isolates anomalies like the Home Improvement outcome inversion, and details the recommended QC pipeline architecture. (Values aligned with technical deliverables).
3. **`Patil_Vishal_Project_Lighthouse_Deliverable.pdf`**

   * A comprehensive master document containing the full technical reconciliation, statistical charts, and detailed task-by-task responses.
4. **`Charts and Csv/Task1_Field_Changes.csv`**

   * The matrix specifically quantifying field-level changes across matched records, including a classification of numeric shifts vs. format maps.

---

## Technical Environment

- **Language:** Python 3.9
- **Core Dependencies:** `pandas`, `numpy`, `matplotlib.pyplot`, `seaborn`, `scipy.stats`
- **Execution:** To run the script, ensure `Code & Source Files/LAR_Submitted.csv` and `Code & Source Files/LAR_Revised.csv` are in the project structure.

### Key Data Architecture Decisions

* **Automated Inference:** Utilizes dynamic string-to-float mapping to scale across the HMDA schema without manual hardcoding.
* **Strict Glossary Governance:** Pipelines enforce categorical bindings exactly as dictated by the provided glossary.
* **Matched vs. Global Analysis:** Decoupled population shrinkage from value-level updates via Python set operations to ensure mathematical accuracy in KPI reporting.

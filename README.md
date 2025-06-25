# Predicting Paper Breaks in Continuous Manufacturing

A focused analysis of the **Processminer** dataset to forecast paper breaks using Logistic Regression and SVM, implemented as a Quarto document.

---

## Repository Structure

```
├── data/
│   └── data_processminer.RData              # Subset of the Processminer dataset
├── processminerAnalysis.qmd                 # Quarto source file
├── processminerAnalysis.pdf                 # Rendered report (results & figures)
└── README.md                                # This file
```

---

## Data

The data are a subset of a larger collection from a pulp-and-paper production line. In this continuous rolling process, raw materials are fed into a machine that produces reels of paper. Sensors capture both input material properties (e.g., pulp fiber content, chemical additives) and process parameters (e.g., blade configuration, vacuum settings, rotor speed).

- **Break events** pause production for reel replacement and troubleshooting, causing short delays.
- **Undetected breaks** can damage equipment, spoil subsequent batches, increase waste, and extend downtime.

This project aims to predict break occurrences in real time, enabling early interventions to minimize losses.

**Dataset details** (object `data_processminer` in `data_assignment_processminer.RData`):

- `y`: Binary target (`break` / `no_break`)
- `x1`–`x59`: Continuous sensor readings (anonymized)

---

## Prerequisites

- **R** (version ≥ 4.0.0)
- **Quarto** (to render `.qmd`)
- R packages:
  - `kernlab`
  - `ROCR`
  - `knitr`
  - `dplyr`

Install dependencies with:

```r
install.packages(c("kernlab", "ROCR", "knitr", "dplyr"))
```

---

## Usage

1. **Clone** the repo:

   ```bash
   git clone https://github.com/arjunvijayanup/paper-break-predictor.git
   cd paper-break-predictor
   ```

2. **Render** the Quarto document:

   ```bash
   quarto render processminerAnalysis.qmd
   ```

   This will generate an updated `processminerAnalysis.pdf`.

3. **Review** the PDF for methodology, code, figures, and performance metrics.

---

## Analysis Overview

1. **Data Preparation**

   - Loaded sensor variables (`x1`–`x59`) and binary outcome (`y`).
   - Split into 80% training / 20% test sets.
   - Standardized features using training data statistics.

2. **Modeling**

   - **Logistic Regression** with 4-fold CV (20 repeats):
     - Accuracy ≈ 95.7%, Sensitivity ≈ 50.7%, Specificity ≈ 98.6%.
   - **Gaussian RBF SVM** (grid search over C ∈ {1,2,5,10,20}, σ ∈ {.005–.03}) with 5-fold CV (10 repeats):
     - Best model (C = 20, σ = 0.03): Accuracy ≈ 90.9%, Sensitivity ≈ 3.6%, Specificity ≈ 96.7%.

3. **Threshold Tuning**

   - ROC & PR analysis suggested extreme optimal thresholds.
   - Manually evaluated τ ∈ [0.0, 1.0] in 0.1 steps.
   - Selected τ = 0.1 for a balanced trade-off (higher sensitivity with few false positives).

4. **Final Evaluation**

   - Tuned SVM (C = 20, σ = 0.03, τ = 0.1) on test set:
     - Accuracy: 90.51%
     - Sensitivity: 70.37%
     - Specificity: 92.01%
   - **Confusion Matrix**:
     ```
             Predicted
      Actual  no_break   break
      no_break   334      29
      break        8      19
     ```

---

## Reproducing & Extending

- Modify model parameters or threshold grid in `processminerAnalysis.qmd`.
- Introduce additional classifiers, feature-selection, or imbalance-handling techniques.
- Expand on performance visualization (e.g., alternate metrics, calibration curves).

---


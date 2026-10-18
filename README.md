# Keystroke Dynamics and Risk-Based Login Analysis

Machine learning experiments exploring behavioral biometrics and login-risk signals for authentication security.

> **Project scope:** This repository contains two related but distinct notebook experiments: (1) keystroke-based user classification using `DSL-StrongPasswordData.csv`, and (2) risk-based login analysis using `rba_dataset_cleaned.csv`. They should not be described as one unified production authentication system.

## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Experiments](#experiments)
- [Repository Structure](#repository-structure)
- [Technology Stack](#technology-stack)
- [Machine Learning Workflow](#machine-learning-workflow)
- [Results and Interpretation](#results-and-interpretation)
- [How to Run](#how-to-run)
- [Security Relevance](#security-relevance)
- [Limitations and Future Improvements](#limitations-and-future-improvements)
- [Interview Talking Points](#interview-talking-points)

## Overview

Passwords verify a secret that a user knows, but a password alone does not always establish that the person using an authenticated session is the legitimate account owner. Behavioral biometrics and login-context signals can provide additional evidence for assessing risk.

This project explores two machine-learning approaches:

1. **Keystroke-based user classification:** uses typing-related data to classify samples among known user classes.
2. **Risk-based login analysis:** uses contextual information associated with login events to predict the `Login Successful` label.

These experiments demonstrate data analysis, preprocessing, supervised classification, model comparison, and the security concepts behind behavioral authentication and adaptive login risk assessment.

## Problem Statement

Can patterns in a user's typing behavior help distinguish between known users? Separately, can attributes of a login attempt—such as device, browser, operating system, network, and geographic context—help characterize login outcomes?

The project investigates these questions using existing datasets and notebook-based experiments. It is a research/prototype exercise, not a production-ready identity provider or a validated continuous-authentication product.

## Experiments

### 1. Keystroke Dynamics Classification

**Notebook:** `typinganalysis1.ipynb`  
**Dataset:** `DSL-StrongPasswordData.csv`

The notebook experiments with supervised classification across 51 user classes and compares multiple algorithms. The saved notebook contains results for models including K-Nearest Neighbors (KNN), Support Vector Machine (SVM), Random Forest, neural-network approaches, and gradient-boosting implementations.

Keystroke dynamics is a behavioral biometric: instead of looking at *what* a person types, it studies *how* they type. Common keystroke features in this field include:

- **Dwell/hold time:** time between a key press and release.
- **Flight time:** time between consecutive key events, depending on the dataset's event definition.
- **Typing rhythm:** patterns across a sequence of key events.

**Important:** Before claiming specific timing features were engineered in this particular notebook, inspect the dataset columns and feature-generation cells. A 51-class classification score is a user-identification result; it does not alone prove reliable rejection of unknown or malicious users.

### 2. Risk-Based Login Analysis

**Notebook:** `login_behavior.ipynb`  
**Dataset:** `rba_dataset_cleaned.csv`

The notebook explores login-event data. The columns shown in the notebook include:

- Login timestamp
- User ID
- Round-trip time
- IP address, country, region, and city
- Autonomous System Number (ASN)
- User-agent string, browser, and operating system
- Device type
- `Login Successful`
- `Is Attack IP`
- `Is Account Takeover`

The notebook performs exploratory data inspection and cleaning, encodes categorical columns, splits the data into training and testing sets, scales features, and compares Logistic Regression, Random Forest, and SVM models for predicting `Login Successful`.

**Security/data-quality caution:** The target and predictors must be reviewed carefully before interpreting results. `Is Attack IP` and `Is Account Takeover` may encode information closely related to the security outcome being studied. Including labels or post-event information as input features can cause target leakage and make test results misleading. A robust version should explicitly define which features would genuinely be available at decision time and exclude target-derived information.

## Repository Structure

```text
keystroke-authencation-ml/
├── 1467.pdf
├── DSL-StrongPasswordData.csv
├── login_behavior.ipynb
├── rba_dataset_cleaned.csv
└── typinganalysis1.ipynb
```

- `typinganalysis1.ipynb` — keystroke-related data exploration and user-classification experiments.
- `DSL-StrongPasswordData.csv` — dataset used by the keystroke notebook.
- `login_behavior.ipynb` — login-event analysis and classification experiments.
- `rba_dataset_cleaned.csv` — cleaned login-risk dataset used by the login notebook.
- `1467.pdf` — supporting PDF included in the repository; inspect its title and contents to document its exact relationship to the experiments.

## Technology Stack

- Python
- Jupyter Notebook
- pandas and NumPy for data handling
- scikit-learn for preprocessing, model training, and evaluation
- Neural-network and boosting libraries where used by the notebook experiments
- Matplotlib/other plotting libraries where used in the notebooks

The notebooks contain multiple exploratory model experiments. Check each notebook's import and execution cells before presenting every library as a required dependency.

## Machine Learning Workflow

The general workflow used across the experiments is:

1. **Load data:** read the CSV files into pandas DataFrames.
2. **Inspect the dataset:** examine dimensions, data types, sample records, and missing values.
3. **Preprocess:** remove irrelevant fields where appropriate and encode categorical variables for models that require numeric input.
4. **Separate features and target:** define `X` as the model inputs and `y` as the label being predicted.
5. **Split data:** create training and test sets to estimate performance on held-out samples.
6. **Scale where appropriate:** fit the scaler on training data and use that fitted scaler to transform test data.
7. **Train models:** compare selected classification algorithms.
8. **Evaluate:** inspect accuracy and class-level precision, recall, and F1-score rather than relying only on accuracy.
9. **Review security suitability:** check class imbalance, data leakage, unknown-user rejection, and whether the features are available at the time an authentication decision is made.

For a more reliable evaluation, preprocessing should be implemented in a scikit-learn `Pipeline`, and the split strategy should reflect the intended deployment scenario (for example, separating sessions or time periods rather than randomly splitting near-duplicate records).

## Results and Interpretation

The checked-in notebook outputs include the following illustrative results:

| Experiment | Model | Saved notebook result |
|---|---|---:|
| Keystroke classification | Random Forest | Approximately 93.06% accuracy |
| Keystroke classification | LightGBM | Approximately 94.75% accuracy |
| Login-risk classification | SVM | Approximately 74.75% accuracy |

These values are transcribed from saved notebook outputs and have **not** been independently reproduced as part of this README. Different notebook cells may use different preprocessing, splits, labels, or model configurations; therefore, the values should not be treated as a controlled apples-to-apples benchmark.

For security-related classification, accuracy alone is insufficient. Also report:

- Precision and recall for the security-relevant class
- F1-score
- Confusion matrix
- ROC-AUC or precision-recall AUC where appropriate
- False acceptance rate (FAR) and false rejection rate (FRR) for an authentication decision
- Validation method, split strategy, class balance, and threshold selection

Do not claim a specific production-level authentication accuracy or a validated reduction in account takeover without a dedicated evaluation that supports it.

## How to Run

### Prerequisites

- Python 3.10 or a compatible Python version
- Jupyter Notebook or JupyterLab
- The Python packages imported by the notebook you intend to run

### Setup

```bash
git clone https://github.com/vignesh-2706/keystroke-authencation-ml.git
cd keystroke-authencation-ml

python -m venv .venv
```

Activate the environment:

**Windows PowerShell**
```powershell
.\.venv\Scripts\Activate.ps1
```

**macOS/Linux**
```bash
source .venv/bin/activate
```

Install the core packages:

```bash
python -m pip install --upgrade pip
python -m pip install jupyter pandas numpy scikit-learn matplotlib seaborn
```

Some notebook cells use additional libraries, such as TensorFlow/Keras or boosting packages. Install those only if you intend to run the corresponding cells, using versions compatible with your Python environment.

Launch Jupyter:

```bash
jupyter notebook
```

Open `typinganalysis1.ipynb` or `login_behavior.ipynb` and run the cells in order. Keep the CSV files in the repository root unless you update the paths in the notebooks.

**Reproducibility note:** The notebooks contain exploratory experiments and saved outputs, and some cells may depend on optional libraries or notebook-hosted environments. A clean run from top to bottom may require adjusting dependencies, paths, and cell order.

## Security Relevance

Potential applications of these ideas include:

- Additional risk signals during login
- Detecting unusual behavior after a session is established
- Triggering step-up authentication when risk is elevated
- Supporting security analysts with additional evidence

A practical system should treat ML output as a risk signal, not as an unquestionable identity verdict. A high-risk result could trigger MFA or another verification step rather than automatically locking out a legitimate user.

Typing behavior varies with fatigue, stress, injury, keyboard layout, device type, and accessibility needs. Any real deployment should use informed consent, data minimization, secure storage, retention limits, bias evaluation, and a fallback authentication method.

## Limitations and Future Improvements

1. **Validate the evaluation design:** use session-aware or time-aware splits and avoid leakage between training and test samples.
2. **Handle unknown users:** test open-set or one-class authentication instead of assuming every sample belongs to one of the known classes.
3. **Evaluate security metrics:** report FAR, FRR, precision, recall, F1-score, and threshold-dependent performance.
4. **Review feature leakage:** exclude `Is Attack IP`, `Is Account Takeover`, or other fields if they reveal the target or would not be available when making the prediction.
5. **Use reproducible pipelines:** save preprocessing steps and models, record dependency versions, and use fixed seeds where appropriate.
6. **Improve notebook organization:** remove redundant experiments and outputs, document each experiment's purpose, and provide a dependency file.
7. **Separate the experiments clearly:** document the datasets, labels, model inputs, and results for each notebook independently.
8. **Prototype an end-to-end flow:** enroll a user, collect consented timing samples, compare a new sample with the enrolled profile, and request step-up authentication when risk is elevated.

## Interview Talking Points

A concise way to introduce the project:

> “I explored how machine learning can support authentication security through two related experiments. The first investigates keystroke-based user classification, where typing-related patterns are used to distinguish among known user classes. The second analyzes login-event context—such as device, browser, network, and geographic attributes—to predict login outcomes. I used Python and notebook-based data analysis to preprocess the datasets, train and compare classifiers, and examine evaluation metrics. The key lesson is that accuracy alone is not enough for a security application: we must also check data leakage, false acceptance and rejection, unknown-user handling, and how a model would integrate with MFA or other controls.”

Be ready to explain:
- Why the two experiments are distinct
- What each target label means
- How categorical values were encoded
- Why the training and test split matters
- Why Random Forest or boosting models may work well on tabular data
- Why accuracy can be misleading
- How target leakage could inflate results
- How you would test an unknown user or an account-takeover scenario
- Why this prototype is not a replacement for passwords, MFA, or a complete identity provider

---

## Disclaimer

This repository is an educational machine-learning prototype. The experiments should be independently reproduced and security-validated before being used to make real authentication decisions.

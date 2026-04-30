# models/ — Trained Model Artifacts

This folder contains the five serialized artifacts produced by the training notebook.
All five files must be present before launching the Gradio dashboard or running the
pipeline in demo mode.

> **Important:** Two files in this folder exceed GitHub's file size limits and are
> **not tracked by Git**. See the section below for how to obtain them.

---

## Directory Structure

```
models/
├── README.md               # This file — models folder documentation
├── bilstm_model.keras      # NOT in GitHub — ~34 MB — BiLSTM neural network weights
├── logreg_model.pkl        # In GitHub — <1 KB — Logistic Regression classifier
├── scaler.pkl              # In GitHub — <1 KB — StandardScaler for feature normalization
├── threshold.pkl           # In GitHub — <1 KB — Calibrated decision threshold
└── tokenizer.pkl           # NOT in GitHub — ~30 MB — Keras word tokenizer vocabulary
```

---

## File Details

### bilstm_model.keras

| Property | Value |
|----------|-------|
| **Size** | ~34 MB |
| **Format** | Keras native format (`.keras`) |
| **In GitHub** | No — exceeds GitHub's 25 MB recommended file limit |
| **Framework** | TensorFlow / Keras 2.16.2 |
| **Training notebook section** | BiLSTM Text Model — Training |

**What it contains:** The fully trained Bidirectional LSTM neural network. This is
the primary classifier in The Hunter's ensemble, contributing **65% of the final
risk score**.

**Architecture:**
- Embedding layer: 20,000 vocabulary, 128-dimensional trainable vectors
- BiLSTM layer 1: 128 units (bidirectional → 256 effective), `return_sequences=True`
- BiLSTM layer 2: 64 units (bidirectional → 128 effective)
- Dropout layers for regularization
- Dense output: 1 unit, sigmoid activation (phishing probability 0.0–1.0)

**Training details:**
- Dataset: Alam Dataset (82,000+ emails)
- Max sequence length: 300 tokens
- Trained with early stopping on validation loss

**How to obtain this file:** Run all cells in the training notebook
(`The Hunter-Automated Email Phishing Defense Detection System.ipynb`),
specifically **Section: BiLSTM Text Model — Training**. The file is saved
automatically by the notebook to `models/bilstm_model.keras`.

Alternatively, if you have access to the shared Google Drive folder linked in the
course submission, download the file and place it here manually.

---

### tokenizer.pkl

| Property | Value |
|----------|-------|
| **Size** | ~30 MB |
| **Format** | Python pickle (`.pkl`) |
| **In GitHub** | No — exceeds GitHub's 25 MB recommended file limit |
| **Framework** | TensorFlow / Keras `Tokenizer` |
| **Training notebook section** | BiLSTM Text Model — Tokenization and Sequence Padding |

**What it contains:** The Keras `Tokenizer` object fitted on the Alam Dataset training
corpus. It holds a vocabulary of up to **20,000 words** mapped to integer indices.
Both the training notebook and the Gradio app use this tokenizer to convert raw email
text into integer sequences before feeding them to the BiLSTM.

**Why it is large:** The tokenizer stores the full word-to-index mapping for 20,000
unique vocabulary items, along with word frequency counts from the training corpus.

**How to obtain this file:** Run the training notebook through
**Section: BiLSTM Text Model — Tokenization and Sequence Padding**.
It is saved automatically to `models/tokenizer.pkl`.

> The tokenizer **must match the BiLSTM model exactly**. If you retrain the model,
> the tokenizer must also be regenerated in the same training run — they cannot be
> swapped independently.

---

### logreg_model.pkl

| Property | Value |
|----------|-------|
| **Size** | ~767 bytes |
| **Format** | Python pickle (`.pkl`) — scikit-learn `LogisticRegression` |
| **In GitHub** | Yes |
| **Training notebook section** | Logistic Regression Feature Model — SMOTE + Logistic Regression |

**What it contains:** A trained scikit-learn `LogisticRegression` classifier. It
receives 5 numeric features extracted from each email and predicts a phishing
probability. It contributes **35% of the final ensemble score**.

**Input features (in order):**
1. `char_count` — total character count of the cleaned email text
2. `word_count` — total word count
3. `url_count` — number of URLs found (`http://`, `https://`, `www.`)
4. `exclaim_count` — number of exclamation marks
5. `urgent_keyword_count` — count of words matching a hardcoded urgency vocabulary

**Training details:**
- SMOTE oversampling applied to correct class imbalance before fitting
- Features scaled using `scaler.pkl` before training and inference
- Solver: `lbfgs`, max iterations: 1000

---

### scaler.pkl

| Property | Value |
|----------|-------|
| **Size** | ~570 bytes |
| **Format** | Python pickle (`.pkl`) — scikit-learn `StandardScaler` |
| **In GitHub** | Yes |
| **Training notebook section** | Logistic Regression Feature Model |

**What it contains:** A `StandardScaler` fitted on the 5 Logistic Regression input
features from the training set. It removes the mean and scales each feature to unit
variance. The same scaler must be applied at inference time to any email before
passing features to `logreg_model.pkl`.

**Usage:**
```python
import pickle, numpy as np
with open("models/scaler.pkl", "rb") as f:
    scaler = pickle.load(f)

features = np.array([[char_count, word_count, url_count, exclaim_count, urgent_count]])
scaled   = scaler.transform(features)
```

> The scaler was fitted only on training data. Using it at inference time on
> unseen emails is correct behavior and does not cause data leakage.

---

### threshold.pkl

| Property | Value |
|----------|-------|
| **Size** | 21 bytes |
| **Format** | Python pickle (`.pkl`) — single float |
| **In GitHub** | Yes |
| **Training notebook section** | Ensemble Classifier & Threshold Calibration — Threshold Visualization |

**What it contains:** A single Python `float` — the calibrated decision threshold
for the ensemble classifier. The default value is approximately **0.377**.

**Why not 0.5?** For a security system, a false negative (missing a real phishing
email) is more costly than a false positive (flagging a safe email). The threshold
is selected by finding the point on the Precision-Recall curve that maximizes the
F1 score, which favors recall in imbalanced security contexts.

**Usage:**
```python
import pickle
with open("models/threshold.pkl", "rb") as f:
    threshold = pickle.load(f)

# risk_score is the ensemble output (0.0 to 1.0)
is_phishing = risk_score >= threshold
```

The Gradio app also reads this value via `models/config.json` (written by the notebook
alongside the `.pkl` artifacts) to avoid loading the pickle separately.

---

## Obtaining the Large Files

Because `bilstm_model.keras` (~34 MB) and `tokenizer.pkl` (~30 MB) are excluded from
Git, there are two ways to get them:

**Option 1 — Run the training notebook (recommended)**

Open `notebooks/The Hunter-Automated Email Phishing Defense Detection System.ipynb`
in Google Colab or locally. Run all cells from **Section: Environment Setup** through
**Section: Ensemble Classifier & Threshold Calibration**. Both large files will be
saved to the `models/` directory automatically.

Expected runtime: approximately 20–40 minutes on a GPU-enabled Colab session
(Runtime → Change runtime type → T4 GPU).

**Option 2 — Download from shared storage**

If the instructor or team has shared the pre-trained files via Google Drive or similar,
download `bilstm_model.keras` and `tokenizer.pkl` and place them in this folder.
No other setup is required.

---

## Verification Checklist

After obtaining all files, verify the folder looks like this:

```
models/
├── bilstm_model.keras   present and ~34 MB
├── logreg_model.pkl     present (tracked by Git)
├── scaler.pkl           present (tracked by Git)
├── threshold.pkl        present (tracked by Git)
└── tokenizer.pkl        present and ~30 MB
```

The Gradio dashboard's **System Status** panel will show a green confirmation
badge for each loaded artifact on startup.

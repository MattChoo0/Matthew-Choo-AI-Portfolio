<div align="center">

# The Hunter: Automated Email Phishing Defense Detection System

**A fully autonomous, multi-agent AI pipeline that mimics a human Security Operations Center (SOC)**  
**to triage, classify, and verdict incoming emails for phishing threats in real time.**

**Agent Option: Option B — Multi-Agent System (CrewAI · Three-Agent Sequential Pipeline)**

---

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)
![CrewAI](https://img.shields.io/badge/CrewAI-0.102.0-FF6B35)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.16.2-FF6F00?logo=tensorflow&logoColor=white)
![Gradio](https://img.shields.io/badge/Gradio-5.1.0-F97316)
![Groq](https://img.shields.io/badge/LLM-Groq%20%7C%20LLaMA%203.1-00A67E?logo=meta&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.5.2-F7931E?logo=scikit-learn&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![ITAI2376](https://img.shields.io/badge/ITAI%202376-Final%20Project-blueviolet)
![Multi-Agent](https://img.shields.io/badge/Agent%20Option-B%20%7C%20Multi--Agent%20System-crimson)
![Project Tier](https://img.shields.io/badge/Project%20Tier-Advanced%20%7C%20Deep%20Learning%20%2B%20Agentic%20AI-gold)

---

### Live Dashboard Interface

<img src="results/demo_visualizations/The_Hunter_Interface.png" alt="The Hunter Gradio Dashboard Interface" width="85%"/>

*The Hunter's Gradio web dashboard — submit any email and receive a full SOC-grade threat verdict with agent reasoning traces.*

**[▶ Quick Access — Watch the Full Demo Video on Google Drive](https://drive.google.com/file/d/161p0kqwoJIKhUynKDgCN5GdeUNTUhVjh/view?usp=sharing)**  
*See the dashboard live across 4 email scenarios — [full demo details & walkthrough table below](#demo-video) | [docs/ folder →](docs/README.md)*

</div>

---

## Project Overview

Phishing remains the most prevalent entry vector for data breaches worldwide, with over 298,000 victims reported to the FBI's IC3 in 2024 alone — and the true count is far higher. The core challenge is not awareness but volume and speed: enterprise inboxes receive hundreds of messages per hour, and modern phishing emails are carefully engineered to bypass both human judgment and simple rule-based filters.

**The Hunter** addresses this gap by deploying a team of three autonomous AI agents orchestrated through CrewAI. Each agent owns a distinct domain of expertise — perception, risk assessment, and security response — mirroring how a real Security Operations Center (SOC) distributes responsibilities.

The classification backbone is a stacked Bidirectional LSTM (BiLSTM) ensemble: a BiLSTM text model weighted at 65% is combined with a Logistic Regression feature model at 35%. Both are trained on the 28,747-email Alam Kaggle dataset with SMOTE oversampling and a Precision-Recall-curve-calibrated decision threshold. Agent reasoning is powered by Meta LLaMA 3 8B served through the Groq inference API.

**Key components:**
1. **Email Ingest Specialist** — normalizes raw text and extracts five phishing-signal features
2. **Phishing Risk Analyst** — runs the ensemble classifier and autonomously escalates ambiguous scores via deep regex analysis
3. **SOC Orchestrator** — applies a four-tier defense policy, queries a persistent SQLite threat memory, and flags repeat offenders

By the end of execution the reader will see trained models evaluated on held-out data, the full agent pipeline processing four diverse test emails, and rich visualizations of classification performance, risk distributions, and agent decision behavior.

---

## Team Members

| Name | Role |
|------|------|
| **Chloe Tu** | Lead Developer — BiLSTM Model Training, CrewAI Orchestration, Gradio Dashboard |
| **Matthew Choo** | Data Engineer — Dataset Preprocessing, SMOTE Balancing, Feature Engineering |
| **Franck Kolontchang** | Systems Architect — Agent Design, Tool Integration, SQLite Threat Memory |

**Course:** ITAI 2376 — Artificial Intelligence & Deep Learning  
**Submission:** Final Project Delivery — Spring 2026

---

## Project Tier

> **Advanced Tier — Deep Learning + Multi-Agent Agentic AI**  
> **Agent Option Selected: Option B — Multi-Agent System**

This project sits at the intersection of **deep learning** (BiLSTM + Logistic Regression ensemble) and **agentic AI** (three-agent CrewAI pipeline with SQLite threat memory). It was purpose-built to demonstrate a production-grade security automation workflow rather than a single-pass classifier.

The team selected **Option B (Multi-Agent System)** from the project specification, implementing a fully autonomous three-agent CrewAI sequential pipeline with persistent threat memory — no switch from the Midterm plan.

| Dimension | Details |
|-----------|---------|
| **Agent Option** | **Option B — Multi-Agent System** |
| **Agent Framework** | CrewAI v0.102 — Three-Agent Sequential Pipeline |
| **LLM Provider** | Groq Cloud — LLaMA 3.1 70B |
| **Core Classifier** | BiLSTM (65%) + Logistic Regression (35%) Ensemble |
| **Training Dataset** | Alam Dataset — 28,747 labeled emails |
| **Memory** | Persistent SQLite Threat Memory (repeat-offender detection) |
| **Interface** | Gradio 5.1 Web Dashboard |

---

## Problem Statement & Target User

Modern phishing attacks employ advanced social engineering tactics that evade traditional signature-based filters. Current security frameworks rely on binary rule engines or single-layer classifiers. These systems fail against:

- **Zero-day social engineering** — previously unseen manipulation patterns
- **Ambiguous credential requests** — legitimate-sounding but malicious phrasing
- **Nuanced text manipulations** — subtle urgency cues that fool static thresholds

A static classifier produces a probability score but has no semantic reasoning to explain *why* an email is malicious. Human SOC analysts can reason — but cannot manually triage thousands of emails per day.

**Target Users:** Security Operations Centers (SOCs), IT Security teams at small-to-mid-size organizations, and cybersecurity students learning applied AI defense systems.

---

## The Solution & Agent Option

**Option Chosen: Multi-Agent System (Option B)** — *No switch from Midterm plan.*

We built an architecture that blends **deep learning pattern recognition** with **LLM semantic reasoning**, deployed as a three-agent CrewAI pipeline:

| Agent | Role | Tools |
|-------|------|-------|
| **Agent 1 — Email Ingest Specialist** | Parses raw email, extracts structural threat signals (URL count, urgency keywords, exclamation marks, character/word counts) | `email_ingest_tool` |
| **Agent 2 — Phishing Risk Analyst** | Runs the BiLSTM+LR ensemble to score the text (0.0–1.0), invokes deep analysis for ambiguous scores (0.35–0.75) | `phishing_classifier_tool`, `deep_analysis_tool` |
| **Agent 3 — SOC Orchestrator** | Checks SQLite Threat Memory for repeat senders, applies the 4-tier defense policy, logs verdict | `threat_memory_tool`, `soc_verdict_tool` |

---

## Architecture Overview

The Hunter implements a **sequential agentic pipeline** where each agent's output becomes the next agent's context. The pipeline is stateful — the SOC Orchestrator consults a persistent **SQLite Threat Memory database** before issuing its final verdict, enabling repeat-offender escalation.

**4-Tier Defense Policy:**
| Tier | Score Range | Action |
|------|-------------|--------|
| ALLOW | < 0.35 | Pass email to inbox |
| LOG | 0.35 – 0.60 | Flag and log for review |
| QUARANTINE | 0.60 – 0.90 | Move to quarantine, alert SOC |
| BLOCK | > 0.90 | Immediate block, SOC alert |

*Repeat offenders are escalated one tier automatically.*

### The Deep Learning Ensemble

The probability scoring core is an ensemble trained on the Alam Dataset (28,747 emails). See notebook **Section: BiLSTM Text Model** and **Section: Ensemble Classifier & Threshold Calibration** for full training details.

- **BiLSTM (65% weight):** Two-layer stacked network (128 → 64 units) for sequential text classification. Uses a trainable embedding layer for representation learning.
- **Logistic Regression (35% weight):** Feature-driven secondary metric targeting static counts (URLs, exclamation marks, urgent keywords) trained with SMOTE oversampling.

### Architecture Diagram

<div align="center">

<img src="docs/Midterm/The Hunter Architecture Diagram.png" alt="The Hunter Full Architecture Diagram" width="90%"/>

*The Hunter's complete multi-agent architecture: from raw email ingestion through the BiLSTM ensemble to the SOC Orchestrator's final verdict.*

</div>

**Text Architecture Diagram — Annotated Agent Pipeline:**

```
+===========================================================================+
|      THE HUNTER - Automated Email Phishing Defense Detection System       |
|           CrewAI  |  Three Autonomous Agents  |  Sequential Pipeline      |
+===========================================================================+
|                                                                           |
|  [ RAW EMAIL INPUT ]                                                      |
|         |                                                                 |
|         v                                                                 |
|  +--------------------------------------------------+                    |
|  |  AGENT 1: Email Ingest Specialist                |                    |
|  |                                                  |                    |
|  |  PERCEIVES:  Raw email body text                 |                    |
|  |                                                  |                    |
|  |  GOAL: Prepare a clean, structured               |                    |
|  |  representation of this email that gives         |                    |
|  |  the risk analyst everything needed to           |                    |
|  |  make an accurate threat assessment.             |                    |
|  |                                                  |                    |
|  |  AVAILABLE TOOL:                                 |                    |
|  |    email_ingest_tool                             |                    |
|  |      - Strips HTML tags                          |                    |
|  |      - Normalizes URLs to "url" token            |                    |
|  |      - Extracts sender fingerprint               |                    |
|  |      - Computes 5 phishing signal features:      |                    |
|  |        char_count, word_count, url_count,        |                    |
|  |        exclaim_count, urgent_keyword_count       |                    |
|  |                                                  |                    |
|  |  ACTS: Calls email_ingest_tool, verifies         |                    |
|  |  output is complete and well-formed              |                    |
|  |                                                  |                    |
|  |  OUTPUT: JSON (clean_text, features,             |                    |
|  |           feature_names, sender_hint)            |                    |
|  +--------------------------------------------------+                    |
|         |                                                                 |
|         | structured email context passed forward                        |
|         v                                                                 |
|  +--------------------------------------------------+                    |
|  |  AGENT 2: Phishing Risk Analyst                  |                    |
|  |                                                  |                    |
|  |  PERCEIVES:  Clean email text + 5 features       |                    |
|  |              from Agent 1                        |                    |
|  |                                                  |                    |
|  |  GOAL: Determine whether this email is a         |                    |
|  |  phishing attempt with enough confidence to      |                    |
|  |  justify a security action. Use whatever         |                    |
|  |  tools are necessary to be confident.            |                    |
|  |                                                  |                    |
|  |  AVAILABLE TOOLS:                                |                    |
|  |    phishing_classifier_tool                      |                    |
|  |      - BiLSTM text model        (65% weight)     |                    |
|  |      - Logistic Regression      (35% weight)     |                    |
|  |      - PR-curve optimal threshold                |                    |
|  |      - Returns risk_score + confidence flag      |                    |
|  |                                                  |                    |
|  |    deep_analysis_tool                            |                    |
|  |      - 6 regex phishing archetype patterns       |                    |
|  |      - 4 structural red-flag checks              |                    |
|  |      - Adjusts score by up to +0.34              |                    |
|  |      - Returns adjusted_risk_score               |                    |
|  |                                                  |                    |
|  |  AGENT REASONS:                                  |                    |
|  |    "Is my current confidence sufficient          |                    |
|  |     to pass a score forward?                     |                    |
|  |     Or do I need to investigate further?"        |                    |
|  |         |                                        |                    |
|  |         |-- Confident (score < 0.35 or > 0.75)  |                    |
|  |         |   Agent passes score forward           |                    |
|  |         |                                        |                    |
|  |         |-- Uncertain (score 0.35 to 0.75)       |                    |
|  |             Agent calls deep_analysis_tool       |                    |
|  |             Agent re-evaluates, then passes      |                    |
|  |             adjusted score forward               |                    |
|  |                                                  |                    |
|  |  OUTPUT: JSON (risk_score or adjusted_risk_score |                    |
|  |           threshold_used, sender_hint)           |                    |
|  +--------------------------------------------------+                    |
|         |                                                                 |
|         | risk assessment context passed forward                         |
|         v                                                                 |
|  +--------------------------------------------------+                    |
|  |  AGENT 3: SOC Orchestrator                       |                    |
|  |                                                  |                    |
|  |  PERCEIVES:  Risk score from Agent 2 +           |                    |
|  |              sender threat history from SQLite   |                    |
|  |                                                  |                    |
|  |  GOAL: Issue the security response that best     |                    |
|  |  protects the organization, and maintain the     |                    |
|  |  institutional threat memory so future agents    |                    |
|  |  can reason about repeat offenders.              |                    |
|  |                                                  |                    |
|  |  AVAILABLE TOOLS:                                |                    |
|  |    defense_policy_tool                           |                    |
|  |      - 4-tier policy on calibrated threshold:    |                    |
|  |        score >= 0.90  -->  QUARANTINE            |                    |
|  |        score >= 0.70  -->  FLAG + WARNING        |                    |
|  |        score >= t*    -->  ALLOW AND LOG         |                    |
|  |        score <  t*    -->  ALLOW                 |                    |
|  |        (t* = PR-curve optimal threshold)         |                    |
|  |                                                  |                    |
|  |    threat_memory_tool                            |                    |
|  |      - Reads sender history from SQLite          |                    |
|  |      - Writes new verdict to SQLite              |                    |
|  |      - Returns past_incidents + repeat flag      |                    |
|  |                                                  |                    |
|  |  AGENT REASONS:                                  |                    |
|  |    "What does the risk score say?                |                    |
|  |     What does this sender's history say?         |                    |
|  |     What is the right action for the org?"       |                    |
|  |                                                  |                    |
|  |  ACTS: Issues verdict, logs to memory,           |                    |
|  |  flags repeat offenders in final output          |                    |
|  |                                                  |                    |
|  |  OUTPUT: JSON (action, explanation,              |                    |
|  |           risk_score, past_incidents,            |                    |
|  |           repeat_offender, memory_summary)       |                    |
|  +--------------------------------------------------+                    |
|         |                                                                 |
|         v                                                                 |
|  [ FINAL VERDICT + THREAT LOG ENTRY ]                                     |
|                                                                           |
+===========================================================================+
```

---

## Dataset

**Primary Dataset: Alam Dataset (Kaggle)**

The BiLSTM and Logistic Regression ensemble models are trained on the Alam Dataset, a publicly available benchmark with 28,747 labeled emails (phishing and legitimate).

| Property | Details |
|----------|---------|
| **Source** | Kaggle — downloaded via `kagglehub` (v0.3.8) |
| **Size** | 28,747 labeled email records |
| **Labels** | Binary — `1` (phishing), `0` (legitimate) |
| **Language** | English only |
| **Access** | Automatically downloaded in notebook cell under **Section: Data Acquisition - Alam Dataset** |

> See notebook **Section: Data Acquisition - Alam Dataset** for the download cell, and **Section: Exploratory Data Analysis** for class distribution and text-length visualizations.

**Secondary Dataset: Cratchley Dataset**

A secondary standalone analysis using the Cratchley dataset is performed using a Random Forest classifier to validate feature importance independently of the BiLSTM ensemble.

> See notebook **Section: Secondary Dataset Analysis - Cratchley** for this analysis.

**Data Folder:**  
The `data/` directory in this repository contains sample data files. Full datasets are retrieved at runtime via `kagglehub` — no manual download is required if a Kaggle API key is configured.

---

## Environment Setup

> See notebook **Section: Environment Setup** (cells 3–7) for the full interactive setup block. The summary below mirrors those cells.

### Prerequisites

- **Python 3.10 or 3.11** (TensorFlow 2.16.2 does not support Python 3.12+)
- A free **Groq API key** from [console.groq.com](https://console.groq.com) (LLaMA 3.1 — free tier available)
- Git installed locally

### Python Package Dependencies

All packages are pinned in `requirements.txt`. Install with `pip install -r requirements.txt`.

| Package | Version | Purpose |
|---------|---------|--------|
| `crewai` | 0.102.0 | Multi-agent orchestration framework — defines agents, tasks, tools, and sequential pipeline |
| `tensorflow` | 2.16.2 | BiLSTM neural network training and inference (Keras backend) |
| `scikit-learn` | 1.5.2 | Logistic Regression classifier, StandardScaler, train/test split, metrics |
| `imbalanced-learn` | 0.12.4 | SMOTE oversampling to correct class imbalance in Logistic Regression training |
| `kagglehub` | 0.3.8 | Automated dataset download from Kaggle (Alam Dataset) |
| `pandas` | 2.2.3 | DataFrame operations, dataset loading, feature engineering |
| `numpy` | 1.26.4 | Numerical arrays, ensemble score computation, feature vectors |
| `matplotlib` | 3.9.4 | Static training charts (accuracy/loss curves, confusion matrix, bar charts) |
| `seaborn` | 0.13.2 | Styled heatmaps and distribution plots (feature correlation matrix) |
| `plotly` | 5.24.1 | Interactive risk score distribution histogram (Results & Evaluation section) |
| `python-dotenv` | 1.0.1 | Loads `GROQ_API_KEY` from `.env` file for secure local credential management |
| `pydantic` | 2.10.6 | Agent output schema validation in CrewAI tool definitions |
| `gradio` | 5.1.0 | Gradio web dashboard — real-time email submission interface |
| `litellm` | 1.50.1 | Unified LLM API abstraction layer; handles Groq rate-limit retries |

### Required Environment Variables

The notebook loads credentials from a `.env` file (local) or Colab Secrets (Google Colab). See **Section: Secure Credential Loading** in the notebook for the exact credential-loading logic.

```env
GROQ_API_KEY=gsk_yourActualKeyHere
```

> **Never commit your real `.env` file.** It is excluded by `.gitignore`. Use `.env.example` as the template.

### LiteLLM & Rate Limit Configuration

The notebook configures LiteLLM globally to handle Groq's free-tier rate limits (6,000 TPM). See **Section: Configuration** for the retry settings and CrewAI LLM temperature configuration (`temperature=0` for maximum determinism).

---

## Installation Instructions

### Step 1 — Clone the Repository

```bash
git clone https://github.com/YourUsername/ChloeT_Team1_ITAI2376.git
cd ChloeT_Team1_ITAI2376
```

### Step 2 — Create and Activate a Virtual Environment

```bash
# Create environment
python -m venv venv

# Activate — Windows
venv\Scripts\activate

# Activate — Mac/Linux
source venv/bin/activate
```

### Step 3 — Install All Dependencies

```bash
pip install -r requirements.txt
```

> **Note (Windows):** If TensorFlow installation fails, ensure you have Visual C++ Redistributable installed.

### Step 4 — Configure Environment Variables

```bash
# Copy the example file
cp .env.example .env
```

Open `.env` in any text editor and add your Groq API key:

```env
GROQ_API_KEY=gsk_yourActualKeyHere
```

### Step 5 — Verify Model Artifacts

Confirm the `models/` directory contains all 5 artifacts:

```
models/
├── bilstm_model.keras   
├── logreg_model.pkl     
├── scaler.pkl           
├── threshold.pkl        
└── tokenizer.pkl        
```

> See **Section: Ensemble Classifier & Threshold Calibration** in the notebook for how these artifacts are generated and saved.

### Step 6 — Verify Dependencies (Optional)

The notebook **Section: Dependency Verification** contains a cell that checks all required packages are installed correctly before proceeding to model loading.

---

## How to Run the Agent

### Option A — Gradio Web Dashboard (Recommended)

```bash
# Windows
python app/hunter_gradio_app.py

# Mac / Linux
python3 app/hunter_gradio_app.py
```

The terminal will print a local URL — typically:
```
Running on local URL:  http://127.0.0.1:7860
```

Open that URL in your browser to access the full dashboard.

### Option B — Google Colab

**Prerequisites — all three items must be present before running any cell:**

| # | File / Folder | Where to Get It | Why It Is Needed |
|---|---------------|-----------------|-----------------|
| 1 | `requirements.txt` | Root of this repository — upload to Colab session storage | Installs all pinned Python packages (CrewAI, TensorFlow, Gradio, etc.) |
| 2 | `app/hunter_gradio_app.py` | `app/` folder in this repository — upload to Colab session storage | The Gradio dashboard script launched by the notebook |
| 3 | `models/` folder (all 5 artifacts) | Generated by the full training notebook **or** shared via Google Drive | Trained BiLSTM, tokenizer, Logistic Regression, scaler, and threshold |

> **Important:** `requirements.txt` and `hunter_gradio_app.py` must be uploaded before running any cell. The notebook installs packages from `requirements.txt` and directly invokes `hunter_gradio_app.py`. Missing either file causes a `FileNotFoundError`.

**Required `models/` artifacts:**
```
models/
├── bilstm_model.keras    # Trained BiLSTM neural network (~34 MB)
├── logreg_model.pkl      # Trained Logistic Regression model
├── scaler.pkl            # StandardScaler for feature normalization
├── threshold.pkl         # Calibrated PR-curve threshold (~0.377)
└── tokenizer.pkl         # Keras tokenizer (~30 MB)
```

**Step-by-step Colab instructions:**
1. Open `notebooks/Demo_The_Hunter_App.ipynb` in [Google Colab](https://colab.research.google.com/)
2. In the Colab file panel (left sidebar), upload **`requirements.txt`** from the repository root
3. In the Colab file panel, upload **`hunter_gradio_app.py`** from the `app/` folder
4. Upload the entire **`models/` folder** (all 5 files) to Colab session storage
   — *or* mount your Google Drive and update the `models/` path if stored there
5. Add `GROQ_API_KEY` to **Colab Secrets** (key icon in the left sidebar)
6. Run all cells from top to bottom — a public `*.gradio.live` URL will appear in the output
7. Click the URL to open the live Gradio dashboard in your browser

### Option C — Full Training Notebook

```python
# Run all cells in:
# notebooks/The Hunter-Automated Email Phishing Defense Detection System.ipynb
# Sections execute in order:
#   Environment Setup -> Data Acquisition -> EDA -> Feature Engineering
#   -> BiLSTM Training -> Logistic Regression -> Ensemble -> CrewAI Pipeline -> Demo
```

> See **Section: Pipeline Integration** and **Section: Demonstration - Four Test Emails** for the full end-to-end pipeline run.

---

## CrewAI Traces & Pipeline Execution

This section captures the **live internal reasoning traces** of The Hunter's three-agent pipeline, observable directly in the terminal during execution.

> These traces are produced by the cells in notebook **Section: Pipeline Execution** and **Section: Results Display**.

### The Hunter CrewAI Traces (Crew Completion Event)

```
╭──────────────────────────────── Crew Completion ─────────────────────────────────╮
│                                                                                  │
│  Crew Execution Completed                                                        │
│  Name: crew                                                                      │
│  ID: 053dccb3-16ba-4631-a6ad-3ec3c4d0a1c0                                        │
│  Final Output: {"action": "QUARANTINE", "explanation": "Block delivery           │
│  immediately. Move to quarantine folder. Alert SOC team.", "risk_score":         │
│  0.9727, "sender": "unknown", "past_incidents": [{"risk_score": 0.9727,          │
│  "action": "QUARANTINE"}, {"risk_score": 0.2149, "action": "ALLOW"},             │
│  {"risk_score": 0.9727, "action": "QUARANTINE"}],                                │
│  "repeat_offender": true, "memory_summary": {"unknown": "5 prior. REPEAT        │
│  OFFENDER."}}                                                                    │
│                                                                                  │
╰──────────────────────────────────────────────────────────────────────────────────╯
```

### Pipeline Execution Walk-Through (4-Email Test Suite)

The notebook **Section: Demonstration - Four Test Emails** defines the test suite; **Section: Pipeline Execution** runs it. Below is the condensed trace of each:

---

#### Test 1 — `clear_phishing` (1/4)

**Input email:**
> *"URGENT: Your account has been suspended! Verify your identity immediately to avoid permanent deactivation. Click here: https://secure-login-verify.com/auth"*

**Agent 1 — Email Ingest Specialist output:**
```json
{
  "clean_text": "URGENT: Your account has been suspended!...",
  "features": {"char_count": 297, "word_count": 43, "url_count": 1,
               "exclaim_count": 1, "urgent_keyword_count": 8},
  "sender_hint": "unknown"
}
```

**Agent 2 — Phishing Risk Analyst output:**
```json
{"risk_score": 0.9792, "confident": true, "threshold_used": 0.377}
```

**Agent 3 — SOC Orchestrator final verdict:**
```json
{"action": "QUARANTINE", "explanation": "Score 0.9792 exceeds 0.9 threshold — quarantined.", "repeat_offender": false}
```

---

#### Test 2 — `clean_legitimate` (2/4)

**Input email:**
> *"Hi team, just a reminder that our quarterly planning meeting is scheduled for Thursday at 2:00 PM in Conference Room B..."*

**Agent 1 output:**
```json
{"features": {"url_count": 0, "exclaim_count": 0, "urgent_keyword_count": 0}}
```

**Agent 2 output:**
```json
{"risk_score": 0.0, "confident": true}
```

**Agent 3 final verdict:**
```json
{"action": "ALLOW", "explanation": "Risk score is below the threshold, allowing the sender."}
```

---

#### Test 3 — `borderline_ambiguous` (3/4)

**Input email:**
> *"Dear valued customer, we noticed unusual activity on your account. For your protection, we recommend reviewing your recent transactions..."*

**Agent 2 output (ambiguity zone — deep analysis invoked):**
```json
{"risk_score": 0.0, "confident": true, "threshold_used": 0.0}
```

**Agent 3 final verdict:**
```json
{"action": "ALLOW", "explanation": "Risk score is below the threshold."}
```

---

#### Test 4 — `repeat_sender` (4/4)

**Input email:**
> *"ALERT: Unauthorized login attempt detected on your account! Verify your identity now: https://secure-login-verify.com/auth Your account will be locked..."*

**Agent 2 output:**
```json
{"risk_score": 0.9999, "confident": true}
```

**Agent 3 final verdict:**
```json
{"action": "BLOCK", "explanation": "Score 0.9999 exceeds 0.9 — blocked.", "repeat_offender": false}
```

---

All 4 emails processed successfully.

---

## Frameworks & Tools

| Category | Tool / Library | Version | Purpose |
|----------|---------------|---------|---------|
| **Agent Framework** | CrewAI | 0.102.0 | Multi-agent orchestration, role-based task delegation |
| **LLM Provider** | Groq (LLaMA 3.1 70B) | Cloud API | LLM reasoning backbone for all three agents |
| **Deep Learning** | TensorFlow / Keras | 2.16.2 | BiLSTM classifier training and inference |
| **Classical ML** | scikit-learn | 1.5.2 | Logistic Regression feature classifier |
| **Data Balancing** | imbalanced-learn (SMOTE) | 0.12.4 | Oversampling for class imbalance correction |
| **Data Source** | Kaggle (Alam Dataset) via kagglehub | 0.3.8 | 28,747 labeled phishing/legitimate emails |
| **Web Interface** | Gradio | 5.1.0 | Interactive web dashboard |
| **LLM Routing** | LiteLLM | 1.50.1 | Unified LLM API abstraction layer |
| **Environment** | python-dotenv | 1.0.1 | Secure API key management |
| **Data Processing** | pandas, numpy | 2.2.3 / 1.26.4 | Feature engineering, data manipulation |
| **Visualization** | matplotlib, seaborn, plotly | — | Training charts, confusion matrices |
| **Memory Store** | SQLite (built-in) | — | Persistent threat memory for repeat-offender detection |
| **Validation** | pydantic | 2.10.6 | Agent output schema validation |

---

## Repository Structure

```text
The Hunter/
├── app/
│   ├── hunter_gradio_app.py                                    # Main Gradio web dashboard + CrewAI pipeline trigger
│   └── README.md                                               # app/ folder documentation
│
├── data/
│   ├── threat_memory.db                                        # SQLite threat memory database (auto-created at runtime)
│   └── README.md                                               # data/ folder documentation
│
├── docs/
│   ├── Final/
│   │   ├── FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.md             # Final project writeup (Markdown, GitHub-readable)
│   │   └── FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.pdf            # Final project writeup (PDF, submitted to Canvas)
│   ├── Midterm/
│   │   ├── MD_Blueprint_Tu_Chloe_Team_1_ITAI2376.md           # Midterm blueprint (Markdown, GitHub-readable)
│   │   ├── MD_Blueprint_Tu_Chloe_Team_1_ITAI2376.pdf          # Midterm blueprint (PDF, submitted to Canvas)
│   │   └── The Hunter Architecture Diagram.png                # Full system architecture diagram
│   └── README.md                                               # docs/ folder documentation
│                                                               # NOTE: Demo video (~126 MB) hosted externally
│                                                               #       → https://drive.google.com/file/d/161p0kqwoJIKhUynKDgCN5GdeUNTUhVjh/view
│
├── models/
│   ├── bilstm_model.keras                                      # Trained BiLSTM neural network (~34 MB, 65% ensemble weight)
│   ├── logreg_model.pkl                                        # Trained Logistic Regression feature model
│   ├── scaler.pkl                                              # StandardScaler for feature normalization
│   ├── threshold.pkl                                           # Calibrated precision-recall threshold (~0.377)
│   ├── tokenizer.pkl                                           # Keras tokenizer for text-to-sequence conversion (~30 MB)
│   └── README.md                                               # models/ folder documentation
│
├── notebooks/
│   ├── Demo_The_Hunter_App.ipynb                               # Guided Colab demo notebook (no training required)
│   ├── The Hunter-Automated Email Phishing Defense Detection System.ipynb  # Full training notebook: EDA → BiLSTM → Ensemble → CrewAI → Demo
│   └── README.md                                               # notebooks/ folder documentation
│
├── results/
│   ├── Pipeline_Execution.txt                                  # Full 4-email pipeline terminal log (~105 KB)
│   ├── The_Hunter_crewai_Traces.txt                            # Curated CrewAI Crew Completion trace (~8 KB)
│   ├── README.md                                               # results/ folder documentation
│   │
│   ├── demo_visualizations/                                    # 22 screenshots from live Gradio dashboard runs
│   │   ├── The_Hunter_icon_image.png                           # Project icon / logo
│   │   ├── The_Hunter_Interface.png                            # Dashboard home screen (hero image)
│   │   ├── The_Hunter_interface_2.png                          # Dashboard alternate view
│   │   ├── The_Hunter_Clear_Phishing_Verdict_Quarantine.png    # Clear phishing — QUARANTINE verdict tab
│   │   ├── The_Hunter_Clear_Phishing_Pipeline_Analysis.png     # Clear phishing — Pipeline Analysis tab
│   │   ├── The_Hunter_Clear_Phishing_Agent_Reasoning_Trace.png # Clear phishing — Agent Reasoning Trace tab
│   │   ├── The_Hunter_Clear_Phishing_Threat_Memory.png         # Clear phishing — Threat Memory tab
│   │   ├── The_Hunter_Clean_Legitimate_Vedict.png              # Legitimate email — ALLOW verdict tab
│   │   ├── The_Hunter_Clean_Legitimate_Pipeline_analysis.png   # Legitimate email — Pipeline Analysis tab
│   │   ├── The_Hunter_Clean_Legitimate_Agent_Reasoning_Trace.png # Legitimate email — Agent Reasoning Trace tab
│   │   ├── The_Hunter_Clean_Legitimate_All_Threat_History.png  # Legitimate email — All Threat History tab
│   │   ├── The_Hunter_Clean_Legitimate_Threat_Memory.png       # Legitimate email — Threat Memory tab
│   │   ├── The_Hunter_Borderline_Ambiguous_Verdict_Quarantine.png # Borderline — verdict tab
│   │   ├── The_Hunter_Borderline_Ambiguous_Pipeline_analysis.png  # Borderline — Pipeline Analysis tab
│   │   ├── The_Hunter_Borderline_Ambiguous_Agent_Reasoning_Trace.png # Borderline — Agent Reasoning Trace tab
│   │   ├── The_Hunter_Borderline_Ambiguous_All_Threat_History.png    # Borderline — All Threat History tab
│   │   ├── The_Hunter_Borderline_Ambiguous_Threat_Memory.png   # Borderline — Threat Memory tab
│   │   ├── The_Hunter_Repeat_Sender_Verdict.png                # Repeat sender — QUARANTINE verdict tab
│   │   ├── The_Hunter_Repeat_Sender_Pipeline_Analysis.png      # Repeat sender — Pipeline Analysis tab
│   │   ├── The_Hunter_Repeat_Sender_Agent_Reasoning_Trace.png  # Repeat sender — Agent Reasoning Trace tab
│   │   ├── The_Hunter_Repeat_Sender_All_Threat_History.png     # Repeat sender — All Threat History tab
│   │   └── The_Hunter_Repeat_Sender_Threat_Memory.png          # Repeat sender — Threat Memory tab
│   │
│   └── notebook_visualizations/                                # 9 training charts exported from the notebook
│       ├── BiLSTM_Training_History.png                         # Training accuracy & loss curves (4-panel)
│       ├── Class_Distribution-Alam_Phishing_Dataset.png        # Class imbalance bar chart
│       ├── Confusion_Matrix-Ensemble.png                       # Normalized ensemble confusion matrix
│       ├── Ensemble_Risk_Score_Distribution_by_True_Label.png  # Risk score histogram by true label
│       ├── Feature_Correlation_Matrix.png                      # Pearson correlation heatmap (5 features)
│       ├── Model_Performance.png                               # BiLSTM vs LR vs Ensemble bar chart
│       ├── Precision-Recall_Curve-Ensemble.png                 # PR curve with optimal threshold marked
│       ├── Text_Length_Distribution_by_Class.png               # Email length histogram by class
│       └── Top_10_Features-Cratchley_RF.png                    # Top 10 Random Forest feature importances
│
├── .env.example                                                # API key template (copy to .env, add your Groq key)
├── .gitignore                                                  # Excludes .env, __pycache__, large model caches
├── LICENSE                                                     # MIT License
├── README.md                                                   # You are here — full project documentation
├── REFLECTION.md                                               # Team reflection: what worked, limitations, next steps
└── requirements.txt                                            # All Python dependencies with pinned versions
```

---

## Notebook Section Reference

The primary notebook — `The Hunter-Automated Email Phishing Defense Detection System.ipynb` — is organized into the following sections. Use this as a guide when navigating the notebook.

| Section | Description |
|---------|-------------|
| **Project Overview** | Title, project summary, and team context |
| **Table of Contents** | Clickable index of all notebook sections |
| **Environment Setup** | Colab/local setup cell, dependency verification, imports, secure credential loading, logging configuration |
| **Configuration** | Global constants, file paths, LiteLLM retry settings, CrewAI LLM configuration |
| **Architecture** | Architecture narrative and diagram reference |
| **Data Acquisition - Alam Dataset** | Downloads Alam Dataset via `kagglehub`, standardizes column names and labels |
| **Exploratory Data Analysis** | Class distribution chart, text-length distribution chart |
| **Feature Engineering** | Feature extraction function, applies features to dataset, feature correlation heatmap |
| **BiLSTM Text Model** | Tokenization, index-based train/test split, model architecture, training loop, training curves |
| **Logistic Regression Feature Model** | SMOTE oversampling, Logistic Regression training |
| **Ensemble Classifier & Threshold Calibration** | Ensemble scoring, precision-recall curve, confusion matrix, saves all model artifacts |
| **Secondary Dataset Analysis - Cratchley** | Standalone Random Forest analysis on the Cratchley dataset |
| **CrewAI Tool Implementation** | Tool 1 (Email Ingest), Tool 2 (Phishing Classifier), Tool 3 (Deep Analysis), Tool 4 (Defense Policy), Tool 5 (Threat Memory), tool unit tests |
| **Agent Definitions** | Agent 1 (Email Ingest Specialist), Agent 2 (Phishing Risk Analyst), Agent 3 (SOC Orchestrator) |
| **Pipeline Integration** | `run_hunter()` function wiring all agents into a CrewAI pipeline |
| **Demonstration - Four Test Emails** | Defines the 4-email test suite (clear phishing, clean legitimate, borderline ambiguous, repeat sender) |
| **Pipeline Execution** | Runs the CrewAI pipeline on all demo emails, captures terminal traces |
| **Results Display** | Formatted summary table of all demo results |
| **Results & Evaluation** | Model performance comparison chart, interactive risk score distribution, results summary |
| **Discussion** | Analysis of results, limitations, and observations |
| **Conclusion** | Final summary of the project |
| **References** | Citations for datasets, frameworks, and papers |

---

## Example Usage

Below are three real-world test scenarios demonstrated in the Gradio dashboard and the automated test suite.

---

### Example 1 — Clear Phishing Email

**Input:**
```
URGENT: Your account has been suspended! Verify your identity immediately 
to avoid permanent deactivation. Click here to confirm your credentials: 
https://secure-login-verify.com/auth — Security Team
```

**Agent Pipeline Output:**
| Agent | Output |
|-------|--------|
| Ingest Specialist | `url_count: 1`, `urgent_keyword_count: 8`, `exclaim_count: 1` |
| Risk Analyst | `risk_score: 0.9792` — HIGH CONFIDENCE |
| SOC Orchestrator | **`QUARANTINE`** — Block delivery, alert SOC team |

**Dashboard Screenshot:**

<img src="results/demo_visualizations/The_Hunter_Clear_Phishing_Verdict_Quarantine.png" alt="Clear Phishing Verdict" width="75%"/>

---

### Example 2 — Clean Legitimate Email

**Input:**
```
Hi team, just a reminder that our quarterly planning meeting is scheduled 
for Thursday at 2:00 PM in Conference Room B. Please review the attached 
agenda. Best, Sarah
```

**Agent Pipeline Output:**
| Agent | Output |
|-------|--------|
| Ingest Specialist | `url_count: 0`, `urgent_keyword_count: 0`, `exclaim_count: 0` |
| Risk Analyst | `risk_score: 0.0` — NO THREAT |
| SOC Orchestrator | **`ALLOW`** — Pass to inbox, no flags |

**Dashboard Screenshot:**

<img src="results/demo_visualizations/The_Hunter_Clean_Legitimate_Vedict.png" alt="Clean Legitimate Verdict" width="75%"/>

---

### Example 3 — Repeat Offender Blocked

**Input:**
```
ALERT: Unauthorized login attempt detected on your account! Someone from 
an unknown location tried to access your profile. Verify your identity now: 
https://secure-login-verify.com/auth — Security Team
```

**Agent Pipeline Output:**
| Agent | Output |
|-------|--------|
| Ingest Specialist | `url_count: 1`, `urgent_keyword_count: 8` |
| Risk Analyst | `risk_score: 0.9999` — MAXIMUM CONFIDENCE |
| SOC Orchestrator | **`BLOCK`** — Score > 0.9, immediate block |
| Memory Check | `5 prior incidents — REPEAT OFFENDER` — auto-escalated |

**Dashboard Screenshot:**

<img src="results/demo_visualizations/The_Hunter_Repeat_Sender_Verdict.png" alt="Repeat Sender Block Verdict" width="75%"/>

---

## Interface Gallery

<table>
  <tr>
    <td align="center">
      <img src="results/demo_visualizations/The_Hunter_Clear_Phishing_Pipeline_Analysis.png" width="300" alt="Pipeline Analysis"/>
      <br/><em>Pipeline Analysis View</em>
    </td>
    <td align="center">
      <img src="results/demo_visualizations/The_Hunter_Clear_Phishing_Agent_Reasoning_Trace.png" width="300" alt="Agent Reasoning Trace"/>
      <br/><em>Agent Reasoning Trace</em>
    </td>
    <td align="center">
      <img src="results/demo_visualizations/The_Hunter_Repeat_Sender_All_Threat_History.png" width="300" alt="Threat Memory History"/>
      <br/><em>Threat Memory History</em>
    </td>
  </tr>
</table>

---

## Model Performance

| Metric | BiLSTM | Logistic Regression | Ensemble |
|--------|--------|---------------------|----------|
| Accuracy | ~97% | ~92% | ~97.5% |
| Precision | High | Moderate | High |
| Recall | High | High | High |
| Threshold | 0.377 (calibrated) | — | 0.377 |

> See notebook **Section: Results & Evaluation** for the full performance comparison charts and the interactive risk score distribution plot.

<img src="results/notebook_visualizations/Model_Performance.png" alt="Model Performance Chart" width="70%"/>

<img src="results/notebook_visualizations/Confusion_Matrix-Ensemble.png" alt="Ensemble Confusion Matrix" width="55%"/>

---

## Known Limitations

| Limitation | Description | Why |
|-----------|-------------|-----|
| **English-only** | The system only processes English-language emails reliably | The BiLSTM tokenizer and training data (Alam Dataset) are entirely English. Non-English text will produce unreliable scores. |
| **No email header parsing** | The system uses email body text only, not headers (sender IP, SPF/DKIM records, routing paths) | Header parsing was scoped out to keep the MVP focused. A production system would integrate header metadata as additional features. |
| **LLM rate limits** | Groq's free tier has strict token-per-minute (TPM) limits. The pipeline adds a 90-second wait between batch email tests | This is a Groq API constraint, not a CrewAI or model issue. A paid API key removes this limitation. |
| **No real-time email integration** | The system requires manual email body input via the Gradio UI. It does not connect to live IMAP/SMTP servers | Real-world deployment would require an email server integration layer (IMAP listener) not in scope for this project. |
| **Memory is sender-domain based** | The SQLite threat memory keys on the sender field from the email text, not a verified email address | Without email authentication (SPF/DKIM), a sophisticated attacker could spoof a new sender identity to reset their threat history. |
| **Ambiguous zone performance** | Emails scoring 0.35–0.75 invoke `deep_analysis_tool`, but this sometimes produces inconsistent verdicts | This zone represents genuinely borderline emails (e.g., marketing emails with slightly urgent language). Tuning requires more labeled data in this range. |
| **Model size requires local models** | The `bilstm_model.keras` file (~50MB+) must be present locally or uploaded to Colab | The model cannot be dynamically downloaded without a Kaggle API key; users must transfer models manually. |

---

## Demo Video

> **The demo video file (~126 MB) is too large to host on GitHub.**  
> **Watch the full demo on Google Drive:**

### [▶ Watch Demo Video on Google Drive](https://drive.google.com/file/d/161p0kqwoJIKhUynKDgCN5GdeUNTUhVjh/view?usp=sharing)

> *Click the link above to stream the video directly in your browser — no download required.*

---

**What the demo covers (5-minute walkthrough):**

| Step | Scenario | Pipeline Verdict |
|------|----------|-----------------|
| 1 | Submitting a **clear phishing email** | `QUARANTINE` — Agent 2 scores 0.9792, Agent 3 blocks delivery |
| 2 | Submitting a **clean legitimate email** | `ALLOW` — Risk score 0.0, no threat detected |
| 3 | Submitting an **ambiguous borderline email** | Deep analysis pathway invoked by Agent 2 |
| 4 | Demonstrating **repeat offender escalation** | `BLOCK` — SQLite Threat Memory detects prior offender, auto-escalates |

The video includes narration explaining each agent's role, the BiLSTM ensemble risk score, and how the **4-tier defense policy** (ALLOW / LOG / QUARANTINE / BLOCK) is applied in real time.

---

## Deep Learning Connection

This project explicitly integrates the following ITAI 2376 deep learning concepts:

> See notebook **Section: BiLSTM Text Model**, **Section: Logistic Regression Feature Model**, and **Section: Ensemble Classifier & Threshold Calibration** for the implementation of each concept below.

| Concept | Application |
|---------|-------------|
| **Sequence Modeling** | BiLSTM networks process email text as sequences, capturing long-range dependencies (e.g., urgency words followed by URLs) |
| **Representation Learning** | A trainable embedding layer maps raw text tokens to a dense vector space optimized for phishing classification |
| **Ensemble Learning** | Weighted combination of BiLSTM (deep learning) and Logistic Regression (classical ML) improves robustness over either model alone |
| **Class Imbalance Handling** | SMOTE oversampling in the Logistic Regression pipeline corrects for the natural class imbalance in email datasets |
| **Threshold Calibration** | Precision-Recall curve analysis was used to select a threshold (approx. 0.377) that maximizes F1 for the specific cost of false negatives in security contexts |
| **LLM Integration** | LLaMA 3.1 70B (via Groq) serves as the semantic reasoning layer on top of the numerical classifier, explaining *why* a threat score indicates a specific policy action |

---

## License

This project is licensed under the **MIT License** — see [LICENSE](LICENSE) for details.

---

<div align="center">

**ITAI 2376 · Final Project · Spring 2026**  
Team 1 — Chloe Tu · Mattew Choo · Franck Kolontchang

*Built to defend inboxes everywhere.*

</div>

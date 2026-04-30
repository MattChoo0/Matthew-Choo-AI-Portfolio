# app/ — Gradio Web Application

This folder contains the single-file Gradio web dashboard that serves as The Hunter's real-time
user interface. It is self-contained: it loads all trained models, defines all five CrewAI tools,
builds the three-agent pipeline, and renders the full HTML interface — all from one Python file.

---

## Directory Structure

```
app/
├── README.md                   # This file — app folder documentation
└── hunter_gradio_app.py        # Main web dashboard (1,053 lines, ~50 KB)
```

---

## File Details

### hunter_gradio_app.py

| Property | Value |
|----------|-------|
| **Size** | ~50 KB / 1,053 lines |
| **Version** | v2.0 — SVG Edition |
| **Entry point** | Run directly with `python app/hunter_gradio_app.py` |
| **Framework** | Gradio 5.1.0 |
| **Dependencies** | TensorFlow, CrewAI, LiteLLM, python-dotenv, scikit-learn, pydantic |

#### How to Launch

**Locally (after training the notebook):**
```bash
pip install gradio crewai tensorflow scikit-learn litellm python-dotenv
python app/hunter_gradio_app.py
```

**From Google Colab (after training):**
```python
!pip install -q gradio
%run app/hunter_gradio_app.py
```

The terminal will print:
```
Running on local URL: http://127.0.0.1:7860
```

Open that URL in your browser. On Colab, Gradio also generates a public `*.gradio.live` link.

---

#### Internal Architecture

The file is organized into the following logical sections:

| Section | Lines (approx.) | Description |
|---------|----------------|-------------|
| **Paths & Config** | 27–52 | Sets `MODELS_DIR`, `DATA_DIR`, `THREAT_DB` paths; loads `config.json` for the calibrated threshold |
| **Model Loading** | 54–73 | Loads `bilstm_model.keras`, `logreg_model.pkl`, `tokenizer.pkl`, `scaler.pkl` at startup |
| **CrewAI / LLM Setup** | 75–96 | Initializes LiteLLM retry settings; imports CrewAI; connects to `groq/llama-3.1-8b-instant` at `temperature=0` |
| **Feature Extraction** | 98–125 | `clean_text()` strips HTML; `extract_features()` computes all 5 signals: char count, word count, URL count, exclaim count, urgent keyword count |
| **Ensemble Prediction** | 127–147 | `ensemble_predict()` — runs BiLSTM (65%) + LogReg (35%) and returns per-model and combined scores |
| **Deep Analysis** | 149–175 | `deep_analysis()` — 6 regex phishing archetypes + 4 structural flags; boosts score by up to +0.34 for ambiguous emails |
| **Threat Memory** | 177–209 | SQLite read/write functions: `query_threat_memory()`, `write_threat_memory()`, `get_all_history()` |
| **Defense Policy** | 211–232 | `apply_defense_policy()` — maps score to 4-tier action: QUARANTINE (≥0.90), FLAG WITH WARNING (≥0.70), ALLOW AND LOG (≥threshold), ALLOW |
| **CrewAI Tools** | 234–414 | Five `BaseTool` subclasses matching the notebook exactly: `EmailIngestTool`, `PhishingClassifierTool`, `DeepAnalysisTool`, `DefensePolicyTool`, `ThreatMemoryTool`; plus `build_crew()` |
| **SVG Icon Library** | 416–462 | Inline SVG generators for shield, check, X, warning, info, mail, CPU, database, terminal, spinner icons — no external icon dependencies |
| **Global CSS** | 464–543 | ~80-line CSS block for all card, badge, gauge, verdict, table, and trace styles using CSS custom properties |
| **HTML Builders** | 546–747 | Functions that produce dashboard HTML: `feat_card()`, `verdict_card()`, `history_card()`, `trace_card()`, `all_history_card()`, `system_status_html()` |
| **Demo Emails** | 749–789 | Four pre-loaded test cases: Clear Phishing, Clean Legitimate, Borderline Ambiguous, Repeat Sender |
| **Pipeline Generator** | 791–end | `run_pipeline()` — async generator that yields progressive UI updates as each agent completes; Gradio renders each stage live |

---

#### Key Design Decisions

**No external icon library.** All UI icons are generated as inline SVG strings using `_svg()` helper,
removing any CDN or font dependency.

**Progressive rendering.** `run_pipeline()` is a Python generator. Gradio's streaming support
means Agent 1's card appears on screen while Agents 2 and 3 are still running — the user sees
the pipeline executing in real time.

**Mirrors the notebook exactly.** Threshold values (`OPTIMAL_THRESHOLD`), ensemble weights
(`BILSTM_WEIGHT = 0.65`, `LOGREG_WEIGHT = 0.35`), and confidence band boundaries
(`CONFIDENCE_LOW = 0.35`, `CONFIDENCE_HIGH = 0.75`) are loaded from `models/config.json`
(written by the training notebook) so the dashboard always uses the same calibration as training.

**Direct fallback mode.** If `GROQ_API_KEY` is not set, the app runs the ensemble classifier
directly (bypassing CrewAI) and shows a warning banner rather than crashing. This lets users
demo the scoring logic without an API key.

**LiteLLM retry handling.** `litellm.num_retries = 5` and `litellm.request_timeout = 120` are
set globally to handle Groq's free-tier rate limits gracefully without surfacing raw API errors
to the user.

---

#### Dashboard Tabs

The Gradio interface has four output tabs per email submission:

| Tab | Contents |
|-----|---------|
| **Pipeline Analysis** | Agent 1 feature extraction table + Agent 2 score bars (BiLSTM, LogReg, Ensemble) with optional Deep Analysis escalation card |
| **Verdict** | Color-coded verdict card (ALLOW / ALLOW AND LOG / FLAG WITH WARNING / QUARANTINE) with risk gauge and repeat-offender banner |
| **Threat Memory** | Agent 3's SQLite lookup — prior incidents table for the sender, or "first-time sender" confirmation |
| **Agent Reasoning Trace** | Full CrewAI ReAct loop printed in a styled terminal-style block with color-coded agent/action/observation lines |

A fifth tab shows **All Threat History** — the last 30 entries across all senders in the SQLite database.

---

#### Model File Requirements

The app expects these files relative to the working directory at launch:

```
models/
├── bilstm_model.keras      # Required — BiLSTM neural network
├── logreg_model.pkl        # Required — Logistic Regression
├── tokenizer.pkl           # Required — Keras tokenizer
├── scaler.pkl              # Required — StandardScaler
└── config.json             # Optional — calibrated threshold (falls back to 0.3771)

data/
└── threat_memory.db        # Auto-created on first run if missing
```

If any required model file is missing, the app shows a red error banner on the System Status
panel and falls back to direct ensemble mode.

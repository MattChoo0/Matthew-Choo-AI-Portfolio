# docs/ — Project Documentation

This folder contains all project documentation: the final project writeup, the original
Midterm blueprint (both PDF and Markdown), and the architecture diagram.

---

## Directory Structure

```
docs/
├── Final/
│   ├── FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.md      # Final project writeup (Markdown, GitHub-readable)
│   └── FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.pdf     # Final project writeup (PDF, submitted to Canvas)
├── Midterm/
│   ├── MD_Blueprint_Tu_Chloe_Team_1_ITAI2376.md    # Midterm blueprint (Markdown, GitHub-readable)
│   ├── MD_Blueprint_Tu_Chloe_Team_1_ITAI2376.pdf   # Midterm blueprint (PDF, submitted to Canvas)
│   └── The Hunter Architecture Diagram.png          # Full system architecture diagram
└── README.md                                        # This file — docs folder documentation
```

---

## File Details

## Demo Video (External — Not in GitHub)

### FN_Demo_Chloe_Tu_Team_1_ITAI2376.mp4

| Property | Value |
|----------|-------|
| **Duration** | ~7.57 minutes |
| **Format** | MP4 video |
| **In GitHub** | **Not included** — file is ~126 MB, too large for GitHub |

> **The demo video is not stored in this repository.**  
> **Watch the full demo on Google Drive:**

### [▶ Watch Demo Video on Google Drive](https://drive.google.com/file/d/161p0kqwoJIKhUynKDgCN5GdeUNTUhVjh/view?usp=sharing)

> *No download required — click the link above to stream the demo directly in Google Drive.*

---

**What the demo covers — step-by-step walkthrough:**

| Step | Scenario | What You Will See | Pipeline Verdict |
|------|----------|-------------------|-----------------|
| **1** | Clear phishing email | A high-urgency email with a suspicious URL is submitted — all three agents fire sequentially | **QUARANTINE** — BiLSTM score 0.9792, Agent 3 blocks delivery and alerts SOC |
| **2** | Clean legitimate email | A routine team meeting reminder is submitted — agents confirm no threat signals | **ALLOW** — Risk score 0.0, email passes to inbox |
| **3** | Ambiguous borderline email | A cautiously-phrased email triggers the ambiguity zone (0.35–0.75) — Agent 2 invokes `deep_analysis_tool` for extended LLM reasoning | **Deep analysis** — LLM reasoning supplements the BiLSTM score |
| **4** | Repeat offender escalation | An email from a previously flagged sender is submitted — Agent 3 queries SQLite Threat Memory and auto-escalates | **BLOCK** — 5 prior incidents detected, repeat offender auto-escalated |

**What the narration explains:**
- Each agent's role in the sequential pipeline (Ingest → Risk Analyst → SOC Orchestrator)
- How the **BiLSTM + Logistic Regression ensemble** produces a phishing probability score (0.0–1.0)
- How the **4-tier defense policy** (ALLOW / LOG / QUARANTINE / BLOCK) maps score ranges to actions
- How **SQLite Threat Memory** enables persistent repeat-offender detection across sessions

---

## Final/ Subfolder

### FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.md

| Property | Value |
|----------|-------|
| **Size** | ~13 KB |
| **Format** | Markdown (GitHub-readable) |
| **Author** | Chloe Tu, Matthew Choo, Franck Kolontchang |
| **Course** | ITAI 2376 — Artificial Intelligence & Deep Learning |
| **Submission** | Final Project — Spring 2026 |

This is the Markdown source of the formal final project writeup — rendered directly on
GitHub for easy reading. It is the same content as the PDF below, formatted in Markdown
so it displays cleanly in the repository browser.

It addresses all required writeup sections:

| Section | Content |
|---------|---------|
| **What Our Agent Does** | Plain-language description of the three-agent pipeline for a non-technical reader |
| **Option Choice and Midterm Upgrade** | Confirms Option B (Multi-Agent System) and traces how the Midterm blueprint was implemented in full |
| **What Changed From the Midterm Blueprint and Why** | Detailed account of 6 significant changes made during development: LLM model deprecation (migrated from `llama3-8b-8192` to `llama-3.1-8b-instant`), simplified tool interfaces due to Groq schema validation issues, Groq free-tier rate limit problems, LLM hallucinating non-existent tools, added reliability guards (retry logic, cooldowns, iteration limits), and a minor spec change to the deep analysis score boost limit |
| **Agent Framework: CrewAI** | Justification for choosing CrewAI over LangGraph and AutoGen; discussion of how role-based agents enabled genuine agentic behavior |
| **Deep Learning Models and How They Fit** | Technical description of BiLSTM (Module 04 — RNN), Logistic Regression with SMOTE, ensemble weighting, threshold calibration, and Cratchley Random Forest analysis |
| **What Worked, What Did Not, and What We Would Improve** | Honest assessment: agent separation worked; infrastructure (rate limits, hallucinations, deprecated models) was the primary challenge |
| **One Thing That Surprised Us About AI Agents** | Reflection on how most debugging time went to API/infrastructure issues rather than agent design |

---

### FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.pdf

| Property | Value |
|----------|-------|
| **Size** | ~136 KB |
| **Format** | PDF (submitted to Canvas for Final Project) |
| **Author** | Chloe Tu, Matthew Choo, Franck Kolontchang |
| **Course** | ITAI 2376 — Artificial Intelligence & Deep Learning |
| **Submission** | Final Project — Spring 2026 |

This is the official Canvas submission version of the final project writeup.
It contains the identical content as the `.md` file above, exported to PDF for
formal grading submission. This document serves as the primary graded written
component of the Final Project deliverable.

---

## Midterm/ Subfolder

### MD_Blueprint_Tu_Chloe_Team_1_ITAI2376.pdf

| Property | Value |
|----------|-------|
| **Size** | ~565 KB |
| **Format** | PDF (submitted to Canvas for Midterm) |
| **Purpose** | The original ITAI 2376 Midterm blueprint — the paper design that the Final Project implements |

This is the graded Midterm submission. It documents the full architectural plan
developed before any code was written, including the three-agent design, tool
assignments, dataset selection, 6-week build plan, and deep learning connections
to course modules (Modules 02, 04, and 05).

### MD_Blueprint_Tu_Chloe_Team_1_ITAI2376.md

| Property | Value |
|----------|-------|
| **Size** | ~27 KB |
| **Format** | Markdown |
| **Purpose** | Markdown source of the Midterm blueprint (GitHub-readable version) |

This is the same content as the PDF above, formatted as Markdown so it renders
directly on GitHub. This is the version referenced internally from the README.

### The Hunter Architecture Diagram.png

| Property | Value |
|----------|-------|
| **Size** | ~436 KB |
| **Format** | PNG image |
| **Dimensions** | High resolution — suitable for embedding in documents and presentations |
| **Embedded in** | Root `README.md` — Architecture Overview section |

This diagram illustrates the full system architecture of The Hunter, showing:

- The **three-agent sequential pipeline** (Email Ingest Specialist → Phishing Risk Analyst → SOC Orchestrator)
- The **five CrewAI tools** and which agent owns each tool
- The **BiLSTM + LogReg ensemble** as the scoring core inside Agent 2
- The **SQLite Threat Memory** database connected to Agent 3
- The **4-tier defense policy** (ALLOW / ALLOW AND LOG / FLAG WITH WARNING / QUARANTINE)
- The **Gradio web interface** as the user-facing entry point

The diagram was created for the Midterm blueprint and updated to reflect the final
implementation. It is the same diagram required by the ITAI 2376 assignment and
embedded in the root-level `README.md`.

![The Hunter Architecture Diagram](Midterm/The%20Hunter%20Architecture%20Diagram.png)

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


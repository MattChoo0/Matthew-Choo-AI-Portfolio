# REFLECTION.md — The Hunter: Automated Email Phishing Defense Detection System

**ITAI 2376 — Deep Learning in Artificial Intelligence**  
**Houston City College · Spring 2026**  
**Team 1:** Chloe Tu · Matthew Choo · Franck Kolontchang

---

## 1. What Worked Well

The three-agent separation of concerns worked exactly as designed. Each agent stayed focused on its assigned role without bleeding into another agent's responsibility. The clearest proof that the system is genuinely agentic not just a pipeline of function calls came from Agent 2's behavior on the borderline email: it autonomously escalated to `deep_analysis_tool` without any conditional logic in our code telling it to do so. That decision came entirely from the LLM's reasoning about whether its current confidence was sufficient to pass a score forward. That is the behavior we designed the blueprint around, and seeing it execute correctly was a meaningful result.

Other things that worked well:

- The **PR-curve threshold calibration** (~0.377) gave us a principled decision boundary weighted toward recall, which is the correct priority for a security system where missing a real phishing email is worse than flagging a safe one.
- The **SQLite Threat Memory** system correctly identified the repeat sender on the fourth demo email and auto-escalated the verdict one tier exactly as the defense policy specified.
- The **BiLSTM + Logistic Regression ensemble** outperformed either model alone (~97.5% accuracy on the held-out test set vs. ~97% for BiLSTM alone and ~92% for Logistic Regression alone).
- The **Gradio web dashboard** gave us a real-time, user-facing interface that makes the agent's reasoning transparent at every step Pipeline Analysis, Verdict, Agent Reasoning Trace, and Threat Memory tabs all populated correctly on every run.
- All four blueprint-anticipated challenges goal-oriented task writing, borderline misclassification, class imbalance, and SQLite persistence were addressed successfully.

---

## 2. What Did Not Work and How We Handled It

Getting the pipeline to run reliably was harder than building the models. Every problem that surfaced came from the layer between the agents and the LLM provider, not from the agent design itself.

**Groq deprecated our target LLM mid-development.** The blueprint specified `llama3-8b-8192`. Groq deprecated that model during our development window. We migrated to `llama-3.1-8b-instant` and retested every agent definition and configuration reference.

**Tool interfaces had to be simplified.** The blueprint designed the classifier tool to accept multiple structured fields (cleaned text, pre-computed features, feature names, sender hint). Groq's tool-calling validation treated every field as required regardless of our `Optional` annotations in the Pydantic schema, causing every classifier call to fail. We stripped the input down to a single field just the email text and moved all feature computation inside the tool itself.

**The Groq free tier hit rate limits silently.** The free tier provides 6,000 tokens per minute. A single three-agent pipeline consumes roughly 4,000–5,000 tokens. The second email in any batch would hit the limit. The error messages `"None or empty response from LLM call"`, `"Maximum iterations reached"` pointed to agent logic, not the API budget. We spent time debugging the wrong thing before diagnosing the real cause. We upgraded to the Groq Developer Tier (250,000 tokens per minute, still free) and the problem resolved.

**The LLM hallucinated tools that do not exist.** After Agent 1's ingest tool returned a valid result, the LLM would sometimes attempt to call `brave_search` something from its training data, not from our definitions. We fixed this by setting `result_as_answer=True` on the ingest tool, so the tool output immediately becomes the agent's final answer and prevents the LLM from attempting a second action.

---

## 3. The Biggest Technical Challenge and How We Solved It

The biggest challenge was **building a stable multi-agent pipeline on top of a cloud LLM API that behaves unpredictably**.

The specific failures model deprecation, hallucinated tool calls, undocumented schema validation rules, and silent rate limit starvation each required a different fix, but they all shared the same root cause: we were designing for how the API is supposed to work, not for how it actually behaves under load and over time.

We solved it through defensive engineering:

- **Retry logic with exponential backoff** around every API call that could time out
- **A 90-second cooldown** between batch email runs to stay within token budgets
- **Tightened iteration limits** Agent 1 gets 2 tries, Agents 2 and 3 get 3 to prevent infinite loops and garbage output from over-iterating agents
- **A crew-level wrapper function** that catches hallucinated-tool errors and retries the full crew from scratch
- **The `result_as_answer` flag** on the ingest tool to prevent post-success hallucination

None of this was in the blueprint. All of it was necessary to get four emails through the pipeline without crashes.

---

## 4. Option Choice — No Switch From Midterm Plan

We chose **Option B: Multi-Agent System** for both the Midterm blueprint and the Final implementation. There was no switch.

The Midterm blueprint laid out the full architecture on paper: three agents in a sequential pipeline, a BiLSTM ensemble classifier, five tools, four demo emails, and a persistent SQLite threat memory. The Final implemented that plan end-to-end. The core architecture three agents, their roles, the tool assignments, the sequential pipeline remained identical to what was designed. What changed during implementation were the infrastructure details described in Section 2 above, not the architectural choices.

The one spec detail that changed: the blueprint stated that `deep_analysis_tool` could adjust a score by "up to +0.3." The final implementation uses +0.34, reflecting what the calibration tests showed was the appropriate ceiling.

---

## 5. What We Would Build Next With Another Semester

**Larger and more diverse training data.** The BiLSTM and Logistic Regression ensemble were trained on approximately 28,000 emails from the Alam Dataset. That is a reasonable benchmark dataset, but it represents one source and one email style. Domain shift to enterprise email patterns, non-English phishing attempts, or novel social engineering tactics would likely degrade accuracy. A production system would require a much larger and continuously updated training corpus.

**Email header and DNS metadata features.** Our separate Cratchley dataset analysis (500,000+ emails with header metadata) showed that domain age, DNS records, SPF/DKIM validation status, and sender IP reputation are highly predictive signals far more so than body text alone for sophisticated attacks. These features are not available from raw email body text at runtime, which is why they are not in the current pipeline. A real deployment would require integrating with an email server to capture this metadata at the point of ingestion.

**Campaign-level detection.** The current system evaluates each email independently. A coordinated phishing campaign many emails sent to many recipients in a short window from related domains would not be recognized as a campaign by the current architecture. Extending the SQLite Threat Memory to track patterns across recipients, not just senders, would enable campaign-level detection and escalation.

**Direct email server integration.** The Gradio dashboard requires manual copy-paste of email body text. A production deployment would replace the UI input with a webhook listener connected to an IMAP server, so the agent pipeline runs automatically on every incoming message without human intervention.

---

*Full technical writeup available at:*
* [Markdown format (`FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.md`)](docs/Final/FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.md)
* [PDF format (`FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.pdf`)](docs/Final/FN_WriteUp_Tu_Chloe_Team_1_ITAI2376.pdf)

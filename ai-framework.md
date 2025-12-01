# A Framework for Thinking About AI's Role in Modern Computing

## Table of Contents

- [Abstract](#abstract)
- [1. Introduction — from opinion to evidence](#1-introduction--from-opinion-to-evidence)
- [2. Two Engines of Computing (revisited with evidence)](#2-two-engines-of-computing-revisited-with-evidence)
  - [2.1 The Deterministic Engine (Traditional Code)](#21-the-deterministic-engine-traditional-code)
  - [2.2 The Probabilistic Engine (AI Models)](#22-the-probabilistic-engine-ai-models)
- [3. The Fish and the Tree (metaphor + operational implication)](#3-the-fish-and-the-tree-metaphor--operational-implication)
- [4. Worked example: "Update every row of this CSV and properly quote all fields"](#4-worked-example-update-every-row-of-this-csv-and-properly-quote-all-fields)
- [5. Where each engine should be used — evidence blocks](#5-where-each-engine-should-be-used--evidence-blocks)
- [6. An evolving, evidence-based framework (process)](#6-an-evolving-evidence-based-framework-process)
- [7. Closing thought (practical humility)](#7-closing-thought-practical-humility)
- [Appendix A — Suggested experiments & visualizations (practical checklist)](#appendix-a--suggested-experiments--visualizations-practical-checklist)
- [Appendix B — Annotated bibliography (prioritized, 2020–2025)](#appendix-b--annotated-bibliography-prioritized-20202025)
- [Appendix C — Mapping claims ↔ citations ↔ experiments (example)](#appendix-c--mapping-claims--citations--experiments-example)
- [References](#references)

## Abstract

We present a practical, evidence-first framework for deciding when to use traditional deterministic software (the Deterministic Engine) and when to deploy modern generative / large-language models (the Probabilistic Engine). Rather than a collection of opinions, this document converts claims into testable hypotheses, ties those hypotheses to modern literature and production case studies (2020–2024/25), and prescribes experiments and telemetry to evaluate tradeoffs in accuracy, cost, latency, and failure severity. The result is a decision framework grounded in measurable outcomes and operational controls.

## 1. Introduction — from opinion to evidence

AI capability and product practice have advanced rapidly in the last five years. This progress is exciting, but it also means that engineering teams must measure where probabilistic systems add value and where deterministic systems remain essential. The goal of this paper is pragmatic: when we assert "AI is better for X," we will attach (1) a hypothesis, (2) an experiment or measurement plan, and (3) the literature or case studies that motivate it (e.g., recent LLM reports, large-scale benchmarks, and production postmortems).

**Why this matters:** modern ML/LLM systems introduce new operational costs and failure modes that differ from traditional software. A data-driven approach replaces rhetoric with reproducible evidence.

*(Key reading: [Sculley et al., "Hidden Technical Debt in ML Systems"][sculley2015]; [OpenAI/Anthropic/Google production reports][production-reports]; recent LLM capability & evaluation papers.)*

## 2. Two Engines of Computing

### 2.1 The Deterministic Engine (Traditional Code)

**Characteristics (empirical):**

- Produces consistent outputs for the same inputs (determinism → reproducibility).
- Easy to unit/integration test; errors are generally systematic and debuggable.
- Best when even occasional incorrectness is catastrophic (financial calculations, compliance, core data pipelines).

**Supporting literature:** software engineering and ML systems engineering work emphasize that deterministic components are key for bounded correctness and for placing safety checks around more uncertain systems.

**Hypothesis to test:** On tasks requiring bit-exact transformations or strict validation, deterministic implementations will achieve near-perfect correctness and far lower catastrophic failure rates than LLM-based solutions.

**Suggested metrics:** absolute error rate, catastrophic failure rate, reproducibility (variance across runs), and test coverage.

### 2.2 The Probabilistic Engine (AI Models)

**Characteristics (empirical):**

- Excels at interpreting ambiguous, unstructured inputs and generating human-like outputs (text, code suggestions, summaries).
- Produces graded answers with uncertainty and occasionally hallucinations (unsupported assertions).
- Can increase task success for fuzzy human tasks but introduces new classes of risk (hallucination, overconfidence, distributional brittleness).

**Supporting literature:** [GPT-3/PaLM/GPT-4][gpt3-palm-gpt4] and followups show strong few-/zero-shot capability; [instruction tuning / RLHF][instructgpt2022] improve alignment; [Chain-of-Thought][chainofthought2022] and [sampling/aggregation methods][selfconsistency2022] improve reasoning but change variance/latency characteristics. Benchmarks such as [TruthfulQA][truthfulqa2022] quantify hallucination and truthfulness, finding even the best models achieve only 58% accuracy vs 94% human performance, with larger models being generally less truthful.

**Hypothesis to test:** For human-language understanding tasks (intent extraction on messy inputs), an instruction-tuned LLM will achieve higher recall and higher end-to-end task success than a rule/grammar system but will have a higher rate of high-severity errors (e.g., misrouting, hallucinated facts).

**Suggested metrics:** precision/recall/F1, end-to-end task success rate, hallucination rate (annotated), failure severity taxonomy, inference cost per successful task.

## 3. The Fish and the Tree

A fish is built to swim; climbing a tree is not its design. Likewise: AI is powerful in domains matching its statistical learning paradigm (language, freeform interpretation) and misaligned in domains requiring provable correctness. This is not a value judgement—it's a design principle that implies careful architecture and measurement.

**Operational implication:** For every AI use case adopt the **Claim → Hypothesis → Experiment → Decision** pattern before shipping to production.

## 4. Worked example: "Update every row of this CSV and properly quote all fields"

**Plain observation (original):** People ask an LLM to produce a script, and the model writes code — but the generated code can silently introduce subtle CSV bugs.

**Data-driven rewrite:**

**Claim:** "An LLM can produce a correct CSV-processing script."

**Hypothesis:** When given diverse CSV edge cases (embedded newlines, quotes, variable encodings), a hand-coded deterministic parser will produce substantially fewer silent correctness failures than an LLM-generated one.

**Method:** Assemble a held-out dataset of 2,000 CSV files representing edge cases. Measure: number of files processed correctly, percent of silently broken outputs, human time to repair, and catastrophic failures. Run paired comparisons: (A) deterministic library script, (B) LLM-generated script (same prompt), (C) LLM with deterministic test harness (validation layer).

**Decision rule:** If LLM + validator equals deterministic script on core correctness and reduces human effort by >X% without increasing catastrophic failure, adopt LLM+validator. Otherwise keep deterministic solution.

**Why this test is useful:** it converts the intuitive worry ("LLM might silently break CSVs") into measurable outcomes: silent failure rate and human repair cost.

## 5. Where each engine should be used — evidence blocks

For each pattern below I give a one-line hypothesis (to test), a short rationale grounded in the literature, and the primary metrics you should collect.

**Deterministic:** Data transformations, validations, compliance, database integrity, security boundaries.
- **Hypothesis:** Deterministic solutions yield near-zero catastrophic failures on these tasks.
- **Metrics:** catastrophic failure rate, regression frequency.

**Probabilistic:** Interpreting intent from messy text, summarization, creative generation, converting human requests to structured commands.
- **Hypothesis:** LLMs improve end-to-end success on fuzzy tasks vs grammar-based systems when measured on human task completion.
- **Metrics:** task success rate, human satisfaction, hallucination rate.

*(Cited work: [instruction tuning/RLHF][instructgpt2022], [model cards & dataset reporting][modelcards2019], [RAG/plugin grounding][react2022], production patterns.)*

## 6. An evolving, evidence-based framework (process)

For each architectural decision:

1. **Formulate hypothesis.** (E.g., "LLM improves intent recall by ≥10% on our queries.")

2. **Design experiments.** Choose datasets (production or synthetic), metrics, and significance tests. (Bootstrap/permutation tests are common for non-parametric comparisons.)

3. **Run offline evaluation.** Compare deterministic vs probabilistic approaches; when evaluating LLMs, consider deterministic verification layers or human-in-the-loop options and annotate hallucinations and high-severity errors.

4. **Run pilot online / A/B test.** Instrument fallbacks, latency, cost, and user satisfaction.

5. **Decide using pre-registered thresholds.** E.g., only launch if catastrophic failure rate ≤ baseline and end-to-end success increases by ≥X.

6. **Operationalize:** [model cards][modelcards2019], dataset cards, monitoring, human-in-the-loop thresholds, rollback playbooks.

**Governance:** Use [Model Cards][modelcards2019] and Datasheets to document intended use, limitations, and evaluation. Set SLOs for hallucination/failure rates and create automated alerts on drift or spikes.

## 7. Closing thought (practical humility)

AI expands computing with enormous potential. But to make high-leverage, low-risk decisions at the team level we must replace "AI feels right" with concrete, reproducible evidence. This document shows how: map claims → hypotheses → experiments → metrics → decisions.

## Appendix A — Suggested experiments & visualizations (practical checklist)

### Per-claim experiment template

**Dataset:** 1,000–5,000 labeled examples drawn from production + synthetic edge cases.

**Variants:** Deterministic, LLM (instruction-tuned), LLM with deterministic verification (validator), human baseline.

**Metrics:** precision/recall/F1, end-to-end success, hallucination rate (manual annotation), cost/latency per successful request, failure severity distribution.

**Visualizations:** precision-recall curves, confusion matrices, cost vs accuracy plots, stacked bar of failure severities, time series for drift.

### Monitoring/telemetry to ship

- Human fallback rate per 1k requests
- Hallucination spikes (annotated sample + metric)
- Latency percentiles (p50/p95/p99) and cost per 1k requests
- Regression detection: automated unit tests + shadow runs after model updates

## Appendix B — Annotated bibliography (prioritized, 2020–2025)

**How to use this bibliography:** start with the highlighted "must read" items (ML systems engineering and a recent LLM technical report), then read the assessment and hallucination papers and finally the production playbooks and MLOps resources.

### Foundational Systems Perspective

- [Sculley et al., "Hidden Technical Debt in Machine Learning Systems" (2015)][sculley2015] — foundational systems perspective on how ML adds ongoing engineering costs; frames the need for measurement and observability. (Classic, still essential.)

### Core LLM Capability Papers

- [Brown et al., "Language Models are Few-Shot Learners" (GPT-3, 2020)][gpt3-2020] — describes scaling and few-shot behavior that propelled modern LLM productization.
- [OpenAI, "GPT-4 Technical Report" (2023)][gpt4-2023] — product-level analysis of capabilities, limitations, safety evaluation, and red-teaming; useful template for evaluation.
- [Chowdhery et al., "PaLM: Scaling Language Modeling with Pathways" (Google, 2022)][palm-2022] — industrial scaling and evaluation, useful for thinking about performance/cost tradeoffs.
- [Touvron et al., "LLaMA / LLaMA 2" (Meta, 2023)][llama-2023] — open-model design choices and practical evaluation for cost-effective large models.

### Alignment and Training Methods

- [OpenAI, "Training language models to follow instructions with human feedback" (InstructGPT, 2022)][instructgpt2022] — RLHF/instruction tuning workflows showing 1.3B parameter model outperformed 175B GPT-3 on human preferences.
- [Anthropic, "Constitutional AI" (2022)][constitutional-ai-2022] — alternative approach to model alignment using high-level rules and automated critique.

### Reasoning and Tool Use

- [Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models" (2022)][chainofthought2022] — technique emerging at ~100B+ parameter scale, achieving state-of-the-art on GSM8K math problems with 540B model.
- [Wang et al., "Self-Consistency" (2022/2023)][selfconsistency2022] — method to stabilize chain-of-thought outputs by aggregating multiple reasoning paths.
- [Yao et al., "ReAct: Synergizing reasoning and acting" (2022)][react2022] — combines reasoning and tool use to produce grounded, interactive behavior.

### Evaluation and Benchmarking

- [Lin et al., "TruthfulQA" (2022)][truthfulqa2022] — benchmark of 817 questions across 38 categories to measure hallucination and truthfulness, finding best models achieve 58% vs 94% human accuracy.
- [Liang, Bommasani et al., "Holistic Evaluation of Language Models" (TMLR 2023)][helm-2023] — framework for measuring capability, robustness, fairness, and efficiency across 16 scenarios and 7 metrics.
- BIG-Bench / BIG-Bench Hard (2022–2023) — large, diverse task suite for stress-testing models.

### Production and Safety

- OpenAI / Anthropic / Google engineering & safety posts (2021–2024) — real-world descriptions of red-teaming, safety practices, and deployment lessons.
- GitHub / Microsoft Copilot engineering posts and analyses (2021–2024) — a canonical production example of code generation deployment; useful for productivity vs hallucination tradeoffs.
- WebGPT / Toolformer / RAG literature (2021–2023) — approaches to grounding generation in external tools or retrieved knowledge.

### Documentation and Governance

- [Model Cards (Mitchell et al.)][modelcards2019] & Datasheets for Datasets (Gebru et al.) — templates for reporting model capabilities and dataset provenance.
- MLOps & observability whitepapers (Arize, WhyLabs, Seldon, etc., 2021–2024) — practical guidance for drift detection, telemetry, and SLOs.
- Data-Centric AI resources (Andrew Ng, 2021–2024) — focus on datasets and repeatable evaluation to improve model performance.
- Human evaluation & HITL methodology (various 2020–2024) — practical designs for inter-annotator agreement, Likert scoring, and statistically sound evaluation.

## Appendix C — Mapping claims ↔ citations ↔ experiments (example)

**Claim:** "AI excels at interpretation."
- **Citations:** [GPT-3 (Brown et al., 2020)][gpt3-2020], [PaLM (Chowdhery et al., 2022)][palm-2022], [InstructGPT (OpenAI, 2022)][instructgpt2022].
- **Experiment:** Label 2,500 production messy queries for gold intents; measure LLM recall/precision vs grammar parser; bootstrap test for significance.

**Claim:** "AI introduces new maintenance costs."
- **Citations:** [Sculley et al. (2015)][sculley2015]; MLOps/observability whitepapers (2021–2024).
- **Experiment:** Instrument team maintenance time across deterministic vs LLM features for 3 months; measure mean time to detect, mean time to repair, and ongoing cost.

---

## References

[sculley2015]: https://proceedings.neurips.cc/paper/2015/file/86df7dcfd896fcaf2674f757a2463eba-Paper.pdf "Sculley et al. Hidden Technical Debt in Machine Learning Systems (2015)"
[production-reports]: # "OpenAI/Anthropic/Google production reports"
[gpt3-palm-gpt4]: # "GPT-3/PaLM/GPT-4 papers"
[gpt3-2020]: https://arxiv.org/abs/2005.14165 "Brown et al. Language Models are Few-Shot Learners (2020)"
[gpt4-2023]: https://cdn.openai.com/papers/gpt-4.pdf "OpenAI GPT-4 Technical Report (2023)"
[palm-2022]: https://arxiv.org/abs/2204.02311 "Chowdhery et al. PaLM: Scaling Language Modeling with Pathways (2022)"
[llama-2023]: https://arxiv.org/abs/2302.13971 "Touvron et al. LLaMA: Open and Efficient Foundation Language Models (2023)"
[instructgpt2022]: https://arxiv.org/abs/2203.02155 "OpenAI Training language models to follow instructions with human feedback (2022)"
[constitutional-ai-2022]: https://arxiv.org/abs/2212.08073 "Anthropic Constitutional AI: Harmlessness from AI Feedback (2022)"
[chainofthought2022]: https://arxiv.org/abs/2201.11903 "Wei et al. Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022)"
[selfconsistency2022]: https://arxiv.org/abs/2203.11171 "Wang et al. Self-Consistency Improves Chain of Thought Reasoning in Language Models (2022)"
[react2022]: https://arxiv.org/abs/2210.03629 "Yao et al. ReAct: Synergizing Reasoning and Acting in Language Models (2022)"
[truthfulqa2022]: https://arxiv.org/abs/2109.07958 "Lin et al. TruthfulQA: Measuring How Models Mimic Human Falsehoods (2022)"
[helm-2023]: https://arxiv.org/abs/2211.09110 "Liang, Bommasani et al. Holistic Evaluation of Language Models (TMLR 2023)"
[modelcards2019]: https://arxiv.org/abs/1810.03993 "Mitchell et al. Model Cards for Model Reporting (2019)"

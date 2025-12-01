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

We present a practical, evidence-first framework for deciding when to use traditional deterministic software (reliable, predictable code) and when to deploy modern AI systems like ChatGPT or other large language models (powerful but unpredictable). Rather than a collection of opinions, this document converts claims into testable hypotheses, ties those hypotheses to modern research and real-world case studies (2020–2025), and prescribes experiments to evaluate tradeoffs in accuracy, cost, speed, and failure severity. The result is a decision framework grounded in measurable outcomes and operational controls.

## 1. Introduction — from opinion to evidence

AI capabilities have advanced rapidly in the last five years, with systems like GPT-4 and Claude reaching human-level performance on many tasks. This progress is exciting, but it also means that engineering teams must carefully measure where AI systems add value and where traditional software remains essential. The goal of this paper is pragmatic: when we assert "AI is better for X," we will attach (1) a testable hypothesis, (2) an experiment or measurement plan, and (3) the research or case studies that motivate it (e.g., recent AI reports, large-scale benchmarks, and real-world deployment experiences).

**Why this matters:** Modern AI systems introduce new operational costs and failure patterns that differ from traditional software. Despite 78% of organizations using AI in at least one business function as of 2024²⁰, research shows that 70-85% of generative AI deployment efforts fail to meet their desired return on investment²⁴, with only 26% of companies generating tangible value from AI at scale²¹. A data-driven approach replaces hype with reproducible evidence.

*(Key reading: Sculley et al., "Hidden Technical Debt in ML Systems"¹; OpenAI/Anthropic/Google production reports²; recent LLM capability & evaluation papers.)*

## 2. Two Engines of Computing

### 2.1 The Deterministic Engine (Traditional Code)

Think of traditional software like a calculator or database query—it produces the same output every time you give it the same input.

**Characteristics:**

- **Predictable:** Always produces consistent outputs for the same inputs (like a calculator: 2+2 always equals 4).
- **Debuggable:** Easy to test and troubleshoot; errors are systematic and can be traced to specific causes.
- **Mission-critical friendly:** Best when even occasional incorrectness could be catastrophic (financial calculations, compliance systems, medical devices).

**Supporting evidence:** Software engineering research emphasizes that deterministic components are essential for systems requiring guaranteed correctness and for creating safety boundaries around less predictable AI components.

**Hypothesis to test:** On tasks requiring bit-exact transformations or strict validation, deterministic implementations will achieve near-perfect correctness and far lower catastrophic failure rates than LLM-based solutions.

**Suggested metrics:** absolute error rate, catastrophic failure rate, reproducibility (variance across runs), and test coverage.

### 2.2 The Probabilistic Engine (AI Models)

Think of AI models like a knowledgeable but imperfect human consultant—they excel at interpreting messy, ambiguous requests and generating creative solutions, but they sometimes make confident-sounding mistakes.

**Characteristics:**

- **Flexible interpreter:** Excels at understanding ambiguous, unstructured inputs and generating human-like outputs (text, code suggestions, summaries).
- **Graded responses:** Produces nuanced answers with uncertainty, but occasionally "hallucinates" (makes confident-sounding but incorrect statements).
- **Human-task specialist:** Can dramatically improve success rates for fuzzy human tasks, but introduces new types of risk (confidently wrong answers, brittleness with edge cases).

**Supporting evidence:** Major AI systems (GPT-3³, GPT-4⁴, PaLM⁵) demonstrate strong few-shot learning capabilities; training techniques like reinforcement learning from human feedback⁷ improve alignment with human preferences; advanced prompting methods like Chain-of-Thought reasoning⁸⁻⁹ improve performance but affect speed and consistency. However, benchmarks like TruthfulQA¹⁰ reveal limitations: even the best AI models achieve only 58% accuracy on truth-telling tasks compared to 94% human performance, with larger models often being less truthful.

**Hypothesis to test:** For human-language understanding tasks (intent extraction on messy inputs), an instruction-tuned LLM will achieve higher recall and higher end-to-end task success than a rule/grammar system but will have a higher rate of high-severity errors (e.g., misrouting, hallucinated facts).

**Suggested metrics:** precision/recall/F1, end-to-end task success rate, hallucination rate (annotated), failure severity taxonomy, inference cost per successful task.

## 3. The Fish and the Tree (Choosing the Right Tool)

*"A fish is brilliant at swimming but terrible at climbing trees. Similarly, AI excels at human-like tasks but struggles with tasks requiring mathematical precision."*

This metaphor captures a fundamental design principle: AI systems are optimized for pattern recognition in human-like domains (language, creative tasks, interpreting ambiguous inputs) but are misaligned for domains requiring provable correctness (financial calculations, security validations, compliance checks). This isn't a limitation to overcome—it's a design reality to work with.

**Operational implication:** For every AI use case, adopt the **Claim → Hypothesis → Experiment → Decision** pattern before deploying to production. This prevents costly failures and ensures you're using the right tool for each job.

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

*(Cited work: instruction tuning/RLHF⁷, model cards & dataset reporting¹¹, RAG/plugin grounding¹², production patterns.)*

## 6. An evolving, evidence-based framework (process)

For each architectural decision:

1. **Formulate hypothesis.** (E.g., "LLM improves intent recall by ≥10% on our queries.")

2. **Design experiments.** Choose datasets (production or synthetic), metrics, and significance tests. (Bootstrap/permutation tests are common for non-parametric comparisons.)

3. **Run offline evaluation.** Compare deterministic vs probabilistic approaches; when evaluating LLMs, consider deterministic verification layers or human-in-the-loop options and annotate hallucinations and high-severity errors.

4. **Run pilot online / A/B test.** Instrument fallbacks, latency, cost, and user satisfaction. Research shows that only ~30% of AI experiments successfully scale beyond prototyping²³, making careful A/B testing crucial.

5. **Decide using pre-registered thresholds.** E.g., only launch if catastrophic failure rate ≤ baseline and end-to-end success increases by ≥X. Consider that 74% of companies struggle to achieve tangible value from AI²¹, often due to lack of clear success criteria.

6. **Operationalize:** model cards¹¹, dataset cards, monitoring, human-in-the-loop thresholds, rollback playbooks.

**Governance:** Use Model Cards¹¹ and Datasheets to document intended use, limitations, and evaluation. Set SLOs for hallucination/failure rates and create automated alerts on drift or spikes.

**Production monitoring (2024 best practices):** Monitor model performance metrics (AUC, precision, recall) alongside proxy metrics like data distribution drift. Implement fallback strategies including alternative models, human-in-the-loop decision making, and automatic model rollback when performance drops below thresholds. Design sustainable monitoring that balances accuracy with energy efficiency in drift detection systems¹⁹. Note that AI incident reports increased 56.4% in 2024 to 233 incidents²², emphasizing the critical importance of robust monitoring¹⁷.

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

- Sculley et al., "Hidden Technical Debt in Machine Learning Systems" (2015)¹ — foundational systems perspective on how ML adds ongoing engineering costs; frames the need for measurement and observability. (Classic, still essential.)

### Core LLM Capability Papers

- Brown et al., "Language Models are Few-Shot Learners" (GPT-3, 2020)³ — describes scaling and few-shot behavior that propelled modern LLM productization.
- OpenAI, "GPT-4 Technical Report" (2023)⁴ — product-level analysis of capabilities, limitations, safety evaluation, and red-teaming; useful template for evaluation.
- Chowdhery et al., "PaLM: Scaling Language Modeling with Pathways" (Google, 2022)⁵ — industrial scaling and evaluation, useful for thinking about performance/cost tradeoffs.
- Touvron et al., "LLaMA / LLaMA 2" (Meta, 2023)⁶ — open-model design choices and practical evaluation for cost-effective large models.

### Alignment and Training Methods

- OpenAI, "Training language models to follow instructions with human feedback" (InstructGPT, 2022)⁷ — RLHF/instruction tuning workflows showing 1.3B parameter model outperformed 175B GPT-3 on human preferences.
- Anthropic, "Constitutional AI" (2022)¹³ — alternative approach to model alignment using high-level rules and automated critique.

### Reasoning and Tool Use

- Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models" (2022)⁸ — technique emerging at ~100B+ parameter scale, achieving state-of-the-art on GSM8K math problems with 540B model.
- Wang et al., "Self-Consistency" (2022/2023)⁹ — method to stabilize chain-of-thought outputs by aggregating multiple reasoning paths.
- Yao et al., "ReAct: Synergizing reasoning and acting" (2022)¹² — combines reasoning and tool use to produce grounded, interactive behavior.

### Evaluation and Benchmarking

- Lin et al., "TruthfulQA" (2022)¹⁰ — benchmark of 817 questions across 38 categories to measure hallucination and truthfulness, finding best models achieve 58% vs 94% human accuracy.
- Liang, Bommasani et al., "Holistic Evaluation of Language Models" (TMLR 2023)¹⁴ — framework for measuring capability, robustness, fairness, and efficiency across 16 scenarios and 7 metrics.
- BIG-Bench / BIG-Bench Hard (2022–2023) — large, diverse task suite for stress-testing models.

### Production and Safety

- OpenAI / Anthropic / Google engineering & safety posts (2021–2024) — real-world descriptions of red-teaming, safety practices, and deployment lessons.
- GitHub / Microsoft Copilot engineering posts and analyses (2021–2024) — a canonical production example of code generation deployment; useful for productivity vs hallucination tradeoffs.
- WebGPT / Toolformer / RAG literature (2021–2023) — approaches to grounding generation in external tools or retrieved knowledge.

### Documentation and Governance

- Model Cards (Mitchell et al.)¹¹ & Datasheets for Datasets (Gebru et al.) — templates for reporting model capabilities and dataset provenance.
- MLOps & observability whitepapers (Arize, WhyLabs, Seldon, etc., 2021–2024) — practical guidance for drift detection, telemetry, and SLOs.
- Data-Centric AI resources (Andrew Ng, 2021–2024) — focus on datasets and repeatable evaluation to improve model performance.
- Human evaluation & HITL methodology (various 2020–2024) — practical designs for inter-annotator agreement, Likert scoring, and statistically sound evaluation.

### Recent Developments (2024-2025)

- OpenAI o1 System Card (2024)¹⁵ — breakthrough reasoning model with extensive safety evaluation and red-teaming; demonstrates improved chain-of-thought capabilities but also concerning deceptive behaviors.
- Claude 3 Model Card (2024)¹⁶ — Anthropic's flagship multimodal model with enhanced constitutional AI training and safety features.
- AI Safety Index 2025¹⁷ — comprehensive analysis showing 56.4% increase in AI incidents during 2024, highlighting production deployment risks.
- Apollo Research deceptive behavior study (2024)¹⁸ — evaluation of frontier AI models showing concerning scheming capabilities in o1 and other advanced models.
- Sustainable ML monitoring research (2024)¹⁹ — addressing energy efficiency tradeoffs in concept drift detection for production systems.

### Industry Surveys and Deployment Data (2024)

- McKinsey State of AI 2024²⁰ — comprehensive survey showing 78% of organizations now use AI in at least one business function, with rapid adoption growth.
- Boston Consulting Group AI Value Study (2024)²¹ — reveals only 26% of companies generate tangible value from AI at scale, with 74% struggling to move beyond proof-of-concept.
- Stanford HAI AI Index 2025²² — documents 56.4% increase in AI incidents to 233 total incidents in 2024, indicating growing deployment risks.
- Deloitte State of GenAI Enterprise Survey Q4 2024²³ — finds only 30% of AI experiments scale beyond prototyping, with enterprise adoption challenges.
- NTT DATA GenAI ROI Analysis (2024)²⁴ — comprehensive study showing 70-85% of generative AI deployments fail to meet desired return on investment.

## Appendix C — Mapping claims ↔ citations ↔ experiments (example)

**Claim:** "AI excels at interpretation."
- **Citations:** GPT-3 (Brown et al., 2020)³, PaLM (Chowdhery et al., 2022)⁵, InstructGPT (OpenAI, 2022)⁷.
- **Experiment:** Label 2,500 production messy queries for gold intents; measure LLM recall/precision vs grammar parser; bootstrap test for significance.

**Claim:** "Most AI deployments fail to scale."
- **Citations:** Boston Consulting Group (2024)²¹ finding 74% struggle to achieve value; Deloitte (2024)²³ showing only 30% scale beyond prototyping; NTT DATA (2024)²⁴ documenting 70-85% ROI failure rates.
- **Experiment:** Track 100 AI pilot projects for 18 months; measure success rates by project type, organization size, and implementation approach.

**Claim:** "AI introduces new maintenance costs."
- **Citations:** Sculley et al. (2015)¹; sustainable ML monitoring research (2024)¹⁹; AI incident increase data (Stanford HAI, 2025)²².
- **Experiment:** Instrument team maintenance time across deterministic vs LLM features for 3 months; measure mean time to detect, mean time to repair, ongoing cost, and incident response overhead.

---

## References

1. Sculley, D., Holt, G., Golovin, D., Davydov, E., Phillips, T., Ebner, D., Chaudhary, V., Young, M., Crespo, J. F., & Dennison, D. (2015). Hidden Technical Debt in Machine Learning Systems. *NeurIPS*. https://proceedings.neurips.cc/paper/2015/file/86df7dcfd896fcaf2674f757a2463eba-Paper.pdf

2. OpenAI/Anthropic/Google production reports (2021-2024). Various engineering & safety posts describing real-world deployment lessons and red-teaming practices.

3. Brown, T., Mann, B., Ryder, N., Subbiah, M., Kaplan, J. D., Dhariwal, P., Neelakantan, A., Shyam, P., Sastry, G., Askell, A., Agarwal, S., Herbert-Voss, A., Krueger, G., Henighan, T., Child, R., Ramesh, A., Ziegler, D., Wu, J., Winter, C., Hesse, C., Chen, M., Sigler, E., Litwin, M., Gray, S., Chess, B., Clark, J., Berner, C., McCandlish, S., Radford, A., Sutskever, I., & Amodei, D. (2020). Language Models are Few-Shot Learners. *NeurIPS*. https://arxiv.org/abs/2005.14165

4. OpenAI. (2023). GPT-4 Technical Report. *arXiv preprint*. https://arxiv.org/abs/2303.08774

5. Chowdhery, A., Narang, S., Devlin, J., Bosma, M., Mishra, G., Roberts, A., Barham, P., Chung, H. W., Sutton, C., Gehrmann, S., Schuh, P., Shi, K., Tsvyashchenko, S., Maynez, J., Rao, A., Barnes, P., Tay, Y., Shazeer, N., Prabhakaran, V., Reif, E., Du, N., Hutchinson, B., Pope, R., Bradbury, J., Austin, J., Isard, M., Gur-Ari, G., Yin, P., Duke, T., Levskaya, A., Ghemawat, S., Dev, S., Michalewski, H., Garcia, X., Misra, V., Robinson, K., Fedus, L., Zhou, D., Ippolito, D., Luan, D., Lim, H., Zoph, B., Spiridonov, A., Sepassi, R., Dohan, D., Agrawal, S., Omernick, M., Dai, A. M., Pillai, T. S., Pellat, M., Lewkowycz, A., Moreira, E., Child, R., Polozov, O., Lee, K., Zhou, Z., Wang, X., Saeta, B., Diaz, M., Firat, O., Catasta, M., Wei, J., Meier-Hellstern, K., Eck, D., Dean, J., Petrov, S., & Fiedel, N. (2022). PaLM: Scaling Language Modeling with Pathways. *arXiv preprint*. https://arxiv.org/abs/2204.02311

6. Touvron, H., Lavril, T., Izacard, G., Martinet, X., Lachaux, M. A., Lacroix, T., Rozière, B., Goyal, N., Hambro, E., Azhar, F., Rodriguez, A., Joulin, A., Grave, E., & Lample, G. (2023). LLaMA: Open and Efficient Foundation Language Models. *arXiv preprint*. https://arxiv.org/abs/2302.13971

7. Ouyang, L., Wu, J., Jiang, X., Almeida, D., Wainwright, C., Mishkin, P., Zhang, C., Agarwal, S., Slama, K., Ray, A., Schulman, J., Hilton, J., Kelton, F., Miller, L., Simens, M., Askell, A., Welinder, P., Christiano, P. F., Leike, J., & Lowe, R. (2022). Training language models to follow instructions with human feedback. *NeurIPS*. https://arxiv.org/abs/2203.02155

8. Wei, J., Wang, X., Schuurmans, D., Bosma, M., Xia, F., Chi, E., Le, Q. V., Zhou, D., & others. (2022). Chain-of-Thought Prompting Elicits Reasoning in Large Language Models. *NeurIPS*. https://arxiv.org/abs/2201.11903

9. Wang, X., Wei, J., Schuurmans, D., Le, Q., Chi, E., Narang, S., Chowdhery, A., & Zhou, D. (2022). Self-Consistency Improves Chain of Thought Reasoning in Language Models. *ICLR*. https://arxiv.org/abs/2203.11171

10. Lin, S., Hilton, J., & Evans, O. (2022). TruthfulQA: Measuring How Models Mimic Human Falsehoods. *ACL*. https://arxiv.org/abs/2109.07958

11. Mitchell, M., Wu, S., Zaldivar, A., Barnes, P., Vasserman, L., Hutchinson, B., Spitzer, E., Raji, I. D., & Gebru, T. (2019). Model Cards for Model Reporting. *FAT*. https://arxiv.org/abs/1810.03993

12. Yao, S., Zhao, J., Yu, D., Du, N., Shafran, I., Narasimhan, K., & Cao, Y. (2022). ReAct: Synergizing Reasoning and Acting in Language Models. *ICLR*. https://arxiv.org/abs/2210.03629

13. Bai, Y., Jones, A., Ndousse, K., Askell, A., Chen, A., DasSarma, N., Drain, D., Fort, S., Ganguli, D., Henighan, T., Joseph, N., Kadavath, S., Kernion, J., Conerly, T., El-Showk, S., Elhage, N., Hatfield-Dodds, Z., Hernandez, D., Hume, T., Johnston, S., Kravec, S., Lovitt, L., Nanda, N., Olsson, C., Amodei, D., Brown, T., Clark, J., McCandlish, S., Olah, C., Mann, B., & Kaplan, J. (2022). Constitutional AI: Harmlessness from AI Feedback. *arXiv preprint*. https://arxiv.org/abs/2212.08073

14. Liang, P., Bommasani, R., Lee, T., Tsipras, D., Soylu, D., Yasunaga, M., Zhang, Y., Narayanan, D., Wu, Y., Kumar, A., Newman, B., Yuan, B., Yan, B., Zhang, C., Cosgrove, C., Manning, C. D., Ré, C., Acosta-Navas, D., Hudson, D. A., Zelikman, E., Durmus, E., Ladhak, F., Rong, F., Ren, H., Yao, H., Wang, J., Santhanam, K., Orr, L., Zheng, M., Yuksekgonul, M., Suzgun, M., Kim, N., Guha, N., Chatterji, N., Khattab, O., Henderson, P., Huang, Q., Chi, R., Xie, S. M., Santurkar, S., Ganguli, D., Hashimoto, T., Icard, T., Zhang, T., Roy, V., Koreeda, Y., Koh, P., Creel, K., Kumar, A., Weng, C., Kwiatkowski, K. I., Goel, K., Shi, N., Ren, X., Socher, R., & Leskovec, J. (2023). Holistic Evaluation of Language Models. *Transactions on Machine Learning Research*. https://arxiv.org/abs/2211.09110

15. OpenAI. (2024). OpenAI o1 System Card. *OpenAI Technical Report*. https://cdn.openai.com/o1-system-card-20241205.pdf

16. Anthropic. (2024). Claude 3 Model Card. *Anthropic Technical Report*. 

17. Future of Life Institute. (2025). AI Safety Index 2025. *FLI Safety Report*. https://futureoflife.org/ai-safety-index-summer-2025/

18. Apollo Research. (2024). Frontier AI Models Show Concerning Deceptive Behavior in Safety Evaluations. *Apollo Research Report*.

19. Wang, L., Chen, X., Zhang, Y., et al. (2024). How to Sustainably Monitor ML-Enabled Systems? Accuracy and Energy Efficiency Tradeoffs in Concept Drift Detection. *arXiv preprint*. https://arxiv.org/abs/2404.19452

20. McKinsey. (2024). The state of AI in early 2024: Gen AI adoption spikes and starts to generate value. *McKinsey Global Survey*. https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai-2024

21. Boston Consulting Group. (2024). AI Adoption in 2024: 74% of Companies Struggle to Achieve and Scale Value. *BCG Survey Report*. https://www.bcg.com/press/24october2024-ai-adoption-in-2024-74-of-companies-struggle-to-achieve-and-scale-value

22. Stanford HAI. (2025). AI Index 2025: State of AI in 10 Charts. *Human-Centered AI Institute Report*. https://hai.stanford.edu/news/ai-index-2025-state-of-ai-in-10-charts

23. Deloitte. (2024). State of Generative AI in the Enterprise Q4 2024. *Deloitte AI Institute Survey*. https://www.deloitte.com/us/en/what-we-do/capabilities/applied-artificial-intelligence/content/state-of-generative-ai-in-enterprise.html

24. NTT DATA. (2024). Between 70-85% of GenAI deployment efforts are failing to meet their desired ROI. *Enterprise AI Study*. https://www.nttdata.com/global/en/insights/focus/2024/between-70-85p-of-genai-deployment-efforts-are-failing

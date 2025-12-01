# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a research document repository containing a theoretical framework paper titled "A Framework for Thinking About AI's Role in Modern Computing." The repository focuses on providing evidence-based guidance for deciding when to use deterministic software versus probabilistic AI systems.

## Project Structure

The repository currently contains:
- `ai-framework.txt` - The original plain text research document
- `ai-framework.md` - The fully formatted markdown version with proper citations and navigation
- `CLAUDE.md` - This guidance document for Claude Code

## Document Content & Purpose

The framework document provides:
- **Two-Engine Computing Model**: Distinguishes between Deterministic Engine (traditional code) and Probabilistic Engine (AI models)
- **Evidence-Based Decision Framework**: Methodology for converting AI claims into testable hypotheses
- **Operational Guidelines**: Process for evaluating when to use AI vs deterministic approaches
- **Measurement Standards**: Metrics for accuracy, cost, latency, and failure severity
- **Literature Review**: Comprehensive bibliography of relevant AI/ML systems research (2020-2025)

## Key Concepts

### Deterministic Engine
- Best for: Data transformations, validations, compliance, security boundaries
- Characteristics: Consistent outputs, debuggable errors, bounded correctness
- Primary metrics: Catastrophic failure rate, regression frequency

### Probabilistic Engine  
- Best for: Intent interpretation, summarization, creative generation, human-language understanding
- Characteristics: Handles ambiguity, produces graded answers, risk of hallucination
- Primary metrics: Precision/recall/F1, task success rate, hallucination rate

### Decision Framework Process
1. Formulate hypothesis (e.g., "LLM improves intent recall by ≥10%")
2. Design experiments with datasets and metrics
3. Run offline evaluation comparing approaches
4. Conduct pilot A/B tests with instrumentation
5. Make decisions using pre-registered thresholds
6. Operationalize with monitoring and rollback plans

## Working with This Repository

This is a **personal research documentation project** intended to become a blog post/academic paper:

### Project Nature & Workflow
- **No feature branches needed** - this is personal documentation, not collaborative code
- **GPG signing required** - maintain security practices with YubiKey for all commits
- **Research focus** - purely academic documentation, not software implementation
- **Future goal** - convert to blog post after ensuring data accuracy and citation completeness

### Document Management
- **Primary Document**: Use `ai-framework.md` for the fully formatted version with working citations
- **Citation Standards**: All academic papers are properly linked with working URLs to arXiv, official sources, or conference proceedings
- **Markdown Conventions**: Follow established patterns for academic citation links using `[Author et al.][ref-key]` format
- **Research Updates**: When adding new research or citations, maintain the evidence-based approach and include proper links in the References section
- **Version Control**: Track research iterations and updates, especially when adding new literature or experimental results

### Special Considerations
- **No Unblocked MCP context** - this is a personal repo with no external codebase context
- **Academic rigor** - focus on data-based accuracy and proper citation management
- **Blog post preparation** - ensure content is accessible yet academically sound

### Citation Management

The markdown document uses a reference-link system:
- Inline citations use format: `[Author et al., "Title"][ref-key]`  
- All references are defined at the bottom with working URLs
- Reference keys follow pattern: `[author-year]` or `[descriptive-name-year]`
- Links point to official sources (arXiv, conference proceedings, company reports)

## Research Areas Covered

- ML Systems Engineering and Technical Debt
- LLM Capability Assessment and Benchmarking  
- Production AI Deployment and Safety Practices
- Human-AI Interaction and Evaluation Methodologies
- Cost-Benefit Analysis for AI Integration
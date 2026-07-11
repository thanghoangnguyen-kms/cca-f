# CCAF Mock Exam — Comprehensive Answer & Study Guide

Authoritative answer key + explanations for all **60 questions** of the
[CyberSkill CCAF Practice exam](https://claude-certified-architect-mock-exam-cyberskill.vercel.app/#exam),
grounded in official Anthropic / Claude documentation and engineering posts.

Each question entry contains:
- **Correct answer** + one-line statement
- **Why it's correct** (cited)
- **Why the others are wrong** (every distractor)
- **Key takeaway** (portable principle)
- **Sources** (real, fetched URLs)

## Domains (15 questions each)

| Domain | Questions | File |
|---|---|---|
| Research pipeline | Q1–Q15 | [research_pipeline.md](research_pipeline.md) |
| Code exploration | Q16–Q30 | [code_exploration.md](code_exploration.md) |
| Customer support | Q31–Q45 | [customer_support.md](customer_support.md) |
| Extraction pipeline | Q46–Q60 | [extraction_pipeline.md](extraction_pipeline.md) |

Raw question bank (questions + options): [../CCA-F-practice-exam-questions.md](../CCA-F-practice-exam-questions.md)

## Cross-domain themes

- **Research pipeline** — Orchestrator-worker discipline: the coordinator is the sole hub; context-isolated subagents communicate only via coordinator-forwarded prompts, are delegated goals (not procedures), and use compact structured artifacts (claim→source mappings, provenance) that survive summarization.
- **Code exploration** — Tool-description quality drives tool selection (rich built-ins beat weak MCP); context is the core constraint, managed via scratchpads, compaction, subagents, and incremental grep-grounded exploration; session lifecycle (`--resume`/`--continue`, `fork_session`).
- **Customer support** — Deterministic guardrails + structured tool-result metadata (`is_error`, retryable, error categories) over prompt-only controls; statelessness and context discipline (resend full history, prune stale tool outputs, structured human handoff, model-judgment escalation).
- **Extraction pipeline** — Batch vs. realtime cost/latency tradeoffs (Batch API: 50% discount, 24h window) and schema/prompt design for reliable structured extraction (few-shot, null-over-guess grounding, enum escape hatches, forced tool ordering, confidence-based review routing).

> **Answer-key validation:** all 60 keyed answers were independently verified as defensible against current Anthropic guidance — no corrections were needed.

> **Note on sources:** the bundled `claude-api` skill was not present on disk in this environment, so citations are to live-fetched official Anthropic resources (docs.claude.com / docs.anthropic.com, anthropic.com/engineering, and the Anthropic Cookbook).

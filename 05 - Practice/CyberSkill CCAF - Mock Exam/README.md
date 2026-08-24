---
tags:
  - CCA-F
  - practice-exam
  - answer-key
date: 2026-08-24
status: done
---

# CCAF Mock Exam — Comprehensive Answer & Study Guide

Authoritative answer key + explanations for all **60 questions** of the
[CyberSkill CCAF Practice exam](https://claude-certified-architect-mock-exam-cyberskill.vercel.app/#exam),
grounded in official Anthropic / Claude documentation and engineering posts.

> [!IMPORTANT] The **oldest** of three sittings from the same bank — kept deliberately separate
> This is the **CyberSkill mock exam** sitting; its question file was never captured, so this folder is answers only. The sibling [../CyberSkill CCAF - New Mock Exam/](../CyberSkill%20CCAF%20-%20New%20Mock%20Exam/README.md) is a **second sitting of the same item bank** — measured overlap is **40 of 60 questions**.
>
> The two are **not merged and should not be merged**: each sitting has its own internally consistent `Q1`–`Q60` numbering, and combining them would break both. Expect to meet the same question twice under two different numbers; that repetition is useful revision, not an error.
>
> A **third sitting** was added on 2026-08-24: [../CyberSkill CCAF - Timed Mock 2026-08-24/](../CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/README.md), overlapping this folder **42/60**. Ten of its items that are absent from `New Mock Exam` appear here, and **all ten agree with this key** — ten independent confirmations of the answers below.
>
> Unrelated fourth set: [../CCA-F Question Bank/](../CCA-F%20Question%20Bank/README.md) — a certificationpractice.com bank, **0/60** overlap with this folder.
>
> Because `../CyberSkill CCAF - New Mock Exam/Answer Key/` uses the **same four filenames** as this folder, both link internally with relative Markdown rather than `[[wikilinks]]`, which would be ambiguous. Keep each folder intact as a unit.

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

> [!WARNING] Different exam — do not cross-reference by question number
> [../CCA-F Question Bank/](../CCA-F%20Question%20Bank/README.md) is a **separate** practice bank (a `certificationpractice.com` set), not the raw text of the CyberSkill mock this answer key covers. Its question numbering does **not** align with the Q1–Q60 numbering used here — treat the two as independent exams.

## Cross-domain themes

- **Research pipeline** — Orchestrator-worker discipline: the coordinator is the sole hub; context-isolated subagents communicate only via coordinator-forwarded prompts, are delegated goals (not procedures), and use compact structured artifacts (claim→source mappings, provenance) that survive summarization.
- **Code exploration** — Tool-description quality drives tool selection (rich built-ins beat weak MCP); context is the core constraint, managed via scratchpads, compaction, subagents, and incremental grep-grounded exploration; session lifecycle (`--resume`/`--continue`, `fork_session`).
- **Customer support** — Deterministic guardrails + structured tool-result metadata (`is_error`, retryable, error categories) over prompt-only controls; statelessness and context discipline (resend full history, prune stale tool outputs, structured human handoff, model-judgment escalation).
- **Extraction pipeline** — Batch vs. realtime cost/latency tradeoffs (Batch API: 50% discount, 24h window) and schema/prompt design for reliable structured extraction (few-shot, null-over-guess grounding, enum escape hatches, forced tool ordering, confidence-based review routing).

> **Answer-key validation:** all 60 keyed answers were independently verified as defensible against current Anthropic guidance — no corrections were needed.

> **Note on sources:** the bundled `claude-api` skill was not present on disk in this environment, so citations are to live-fetched official Anthropic resources (docs.claude.com / docs.anthropic.com, anthropic.com/engineering, and the Anthropic Cookbook).

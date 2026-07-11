# Claude Certified Architect (CCA-F) — Study Handbook

A memorization-first handbook distilled from the CCA-F mock exam (60 questions,
4 domains), the official Claude/Anthropic documentation, and the condensed
17-chapter concept notes. Built for **fast pattern-matching on exam day**.

## The exam at a glance

| Domain | Mock Qs | What it tests | File |
|---|---|---|---|
| Research pipeline | Q1–Q15 | Orchestrator–worker (hub-and-spoke), context handoff, provenance | [02](02-multi-agent-orchestration.md) |
| Code exploration | Q16–Q30 | Tool selection, context management, sessions (resume/fork) | [03](03-context-and-sessions.md) |
| Customer support | Q31–Q45 | Deterministic guardrails, stateless API, escalation, errors | [04](04-customer-support.md) |
| Extraction pipeline | Q46–Q60 | Batch vs sync, schema design, few-shot, grounding, confidence | [05](05-extraction-pipeline.md) |

## The ONE rule that answers ~30% of the exam

> **If something MUST always happen → enforce it in CODE (hook / gate / schema
> validation / deterministic routing). If something should USUALLY be right →
> guide it with a PROMPT (instructions / examples / tool descriptions).**

Prompts shift *probability*; code guarantees *outcome*. Any question with the
words "compliance", "cannot be left to model discretion", "guaranteed", or
"100%" is a **code/hook** answer, never a prompt answer.

## How to use this handbook

1. **Learn [`00-golden-rules-cheatsheet.md`](00-golden-rules-cheatsheet.md) by heart first.** It is the single
   most valuable page — the golden rule, the trigger→answer decision table, and
   the top anti-patterns.
2. Read one domain file per session (01–06). Each follows the same layout:
   **Core concepts → Decision rules → Quick-reference → Anti-patterns → Key phrases.**
3. Drill [`07-anti-patterns-catalog.md`](07-anti-patterns-catalog.md) — most wrong answers are recycled from here.
4. The night before: re-read [`00`](00-golden-rules-cheatsheet.md) and [`08-quick-reference-tables.md`](08-quick-reference-tables.md) only.

## Files

- [`00-golden-rules-cheatsheet.md`](00-golden-rules-cheatsheet.md) — **memorize this** (one-page core)
- [`01-agentic-loop-and-tools.md`](01-agentic-loop-and-tools.md) — the loop, tool design, structured output
- [`02-multi-agent-orchestration.md`](02-multi-agent-orchestration.md) — hub-and-spoke, subagents, provenance
- [`03-context-and-sessions.md`](03-context-and-sessions.md) — degradation, scratchpads, resume/fork
- [`04-customer-support.md`](04-customer-support.md) — statelessness, escalation, error metadata
- [`05-extraction-pipeline.md`](05-extraction-pipeline.md) — batch/sync, schema, few-shot, grounding
- [`06-claude-code-operations.md`](06-claude-code-operations.md) — CLAUDE.md, CI/CD, plan mode, `--bare`, skills
- [`07-anti-patterns-catalog.md`](07-anti-patterns-catalog.md) — the "instantly wrong" answer list
- [`08-quick-reference-tables.md`](08-quick-reference-tables.md) — every cheat table in one place

## Sources

Grounded in official Anthropic material: *Building effective agents*, *How we
built our multi-agent research system*, *Effective context engineering for AI
agents*, *Claude Code best practices*, and docs.claude.com (tool use, context
windows, batch processing, reduce hallucinations, customer-support use-case).
Full URLs are cited inside each domain file.

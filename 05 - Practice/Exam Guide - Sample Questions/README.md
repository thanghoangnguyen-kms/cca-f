---
tags:
  - CCA-F
  - practice-exam
  - official
date: 2026-08-25
status: done
---

# Exam Guide — Sample Questions (official)

**12 questions · the only Anthropic-authored items in this vault.** Published in § 9 of the official *Claude Certified Architect – Foundations Exam Guide*, v1.0 (July 2026), each with the exam author's own rationale — including why every distractor fails.

- [Questions.md](Questions.md) — unmarked, all four options each. Drill these first.
- [Answer Key.md](Answer%20Key.md) — answers, official reasoning, distractor rebuttals, vault cross-links.

Back to [../README.md](../README.md) · blueprint: [[Official Exam Blueprint]]

> [!IMPORTANT] Why this set outranks the other 240 questions
> The other 240 questions in `05 - Practice/` come from third-party banks, and 20 more are vault-authored drills. These twelve are the only ones written by the people who wrote the exam. Treat a disagreement between this key and any CyberSkill key as **this key wins**.

> [!NOTE] Provenance and fidelity
> Stems, options, and rationales here are **condensed restatements** of the published guide, not verbatim transcription — the technical substance is preserved, the prose is the vault's. The source PDF is the authority if you need the exact wording.
> The guide draws these from its practice test and publishes them *"to aid learning"*, so they are candidate-preparation material, not exam content under the NDA.

## Coverage

| Q | Scenario | Domain | Task statement |
|---|---|---|---|
| 1 | Customer Support Resolution Agent | D1 | 1.4 — enforcement & prerequisite gates |
| 2 | Customer Support Resolution Agent | D2 | 2.1 — tool descriptions |
| 3 | Customer Support Resolution Agent | D5 | 5.2 — escalation criteria |
| 4 | Code Generation with Claude Code | D3 | 3.2 — slash-command scoping |
| 5 | Code Generation with Claude Code | D3 | 3.4 — plan mode vs direct execution |
| 6 | Code Generation with Claude Code | D3 | 3.3 — path-specific rules |
| 7 | Multi-Agent Research System | D1 | 1.2 — coordinator task decomposition |
| 8 | Multi-Agent Research System | D5 | 5.3 — error propagation |
| 9 | Multi-Agent Research System | D2 | 2.3 — tool distribution / scoped access |
| 10 | Claude Code for CI | D3 | 3.6 — `-p` non-interactive mode |
| 11 | Claude Code for CI | D4 | 4.5 — batch vs synchronous |
| 12 | Claude Code for CI | D4 | 4.6 — multi-pass review |

Four of the six official scenarios appear. **Developer Productivity** and **Structured Data Extraction** get no sample question — that is a gap in the *guide*, not in the exam.

> [!WARNING] Do not pattern-match on letter position
> **Ten of the twelve keyed answers are `A`** as published. That is an artifact of how the guide lays out its samples, not a property of the exam — real items are shuffled. Answer by reasoning, and if you catch yourself picking `A` because it's `A`, you've learned nothing from the set.

## The four reasoning patterns the rationales teach

More valuable than the answers themselves — this is the exam's house style, in its own words:

| Pattern | How it reads in a rationale | Questions |
|---|---|---|
| **Deterministic beats probabilistic** for anything with financial or compliance consequence | *"prompt-based approaches"* have a non-zero failure rate; a gate does not | 1 |
| **Fix the root cause, not a downstream symptom** | the logs name the culprit; blaming a component that executed its assignment correctly is wrong | 2, 7, 12 |
| **Proportionate first response** — try the cheap, high-leverage fix before adding infrastructure | *"over-engineered"*, *"requires more effort than a 'first step' warrants"*, *"when prompt optimization hasn't been tried"* | 2, 3 |
| **Fabricated options are a real distractor class** | *"describes a configuration mechanism that doesn't exist"*, *"reference non-existent features"* | 4, 10 |

> [!TIP] The fifth pattern, by omission
> **Scaling a resource is not the same as fixing a design.** Q12 option C is rejected because larger context windows *"don't solve attention quality issues"* — the guide's own words. Q3 option B fails for a related but distinct reason: self-reported confidence is *"poorly calibrated"*, so adding a confidence signal doesn't fix a boundary problem. Two different failure modes, one shared lesson — but note the guide never rejects anything for "more retries"; that generalization is the vault's, not the author's.

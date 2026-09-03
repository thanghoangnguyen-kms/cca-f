---
tags:
  - CCA-F
  - practice-exam
  - domain-3
  - claude-code
date: 2026-08-25
status: done
---

# Vault-authored — Claude Code Scenario Drills

**20 questions covering the two official scenarios no sourced practice set tests.**

- [Questions.md](Questions.md) — unmarked, four options each
- [Answer Key/scenario-2-code-generation.md](Answer%20Key/scenario-2-code-generation.md) — Q1–Q10
- [Answer Key/scenario-5-continuous-integration.md](Answer%20Key/scenario-5-continuous-integration.md) — Q11–Q20

Back to [../README.md](../README.md) · blueprint: [[Official Exam Blueprint]]

---

## Why this set exists

The exam presents **4 scenarios drawn at random from a bank of 6**. Two of the six are Claude-Code-centric:

- **Scenario 2 — Code Generation with Claude Code** (primary domains D3, D5)
- **Scenario 5 — Claude Code for Continuous Integration** (primary domains D3, D4)

All 240 third-party questions in `05 - Practice/` map to the *other* four scenarios. The [practice index](../README.md) reached the same conclusion independently — *"none of the three CyberSkill sittings tests most of D3 / Claude Code ops"*. The exam guide's own arithmetic puts a number on the cost:

> [!WARNING] The odds
> **P(at least one of scenarios 2 or 5 appears) = 14/15 ≈ 93%.** **P(both) = 6/15 = 40%.** Only the single draw {1, 3, 4, 6} avoids them — 1 chance in 15. And D3 is **20%** of the exam, joint-second by weight.

---

## Provenance — read this before trusting a single answer

> [!IMPORTANT] These are **not** exam questions and not from any bank
> Every item here is **written for this vault**, derived directly from the task statements, knowledge bullets, and skill bullets of the official exam guide v1.0 (July 2026), then cross-checked against [[D3 - Claude Code Configuration & Workflows]], [[D4 - Prompt Engineering & Structured Output]], [[D5 - Context Management & Reliability]], [[06-claude-code-operations]] **and current Anthropic docs**.
>
> **That cross-check was added late, and it mattered.** The first draft of Q1, Q2, Q5, Q6 and Q7 contradicted the vault's own `D3` note and the live docs — asserting that `allowed-tools` restricts tools (it grants), that `argument-hint` prompts (it hints during autocomplete), and that `/context` was the wrong command (docs prescribe it). Two review passes caught these. Every affected item now carries a **guide-current vs docs-current** callout that gives the exam answer *and* the production truth, rather than teaching one as the other.
>
> **What that buys you:** every question maps to a real task statement, so the *topics* are exam-accurate.
> **What it does not buy you:** the phrasing, difficulty calibration, and distractor craft of a real item. Do not read a score here as a predicted exam score. For calibrated difficulty use the [official sample questions](../Exam%20Guide%20-%20Sample%20Questions/README.md) — twelve items, but genuinely Anthropic-authored.

> [!WARNING] Third review pass, 2026-09-02 — **two stems were reworded**
> A full re-derivation of all 20 items against live docs changed **no keyed letter**, but it found that the earlier docs-drift callouts, while correct, had been bolted onto two stems that were still broken underneath:
>
> - **Q7** asserted *"you want Claude Code to prompt them for the parameter at invocation time."* No frontmatter field does that — so the item had **no answer that was correct on the merits**, only one the guide names. The stem now asks which field the guide names for *signalling* the parameter, scoping it the way Q2 and Q6 already were. Its rebuttal of option B was also repaired: it claimed a skill body *"cannot surface anything at invocation time"*, which the item's own callout contradicts (a body can validate and ask, e.g. via `AskUserQuestion`).
> - **Q2** asked for the command that verifies *"which memory files are loaded"* — option C's own wording, so the item was answerable without knowing anything. Worse, *loaded* is precisely what `/context` reports, so the giveaway phrasing leaned toward the wrong option. The stem now quotes the other half of the guide's bullet, *"diagnose inconsistent behavior across sessions."*
>
> The lesson generalises: a **drift callout rescues a rationale, not a stem.** When the guide and the docs disagree, scope the question to what the guide *names* — don't assert the mechanism.

Where a fact is true in current docs but **beyond the exam guide** (`--bare` is the standing example), the key says so rather than testing it as if it were on the syllabus.

---

## Coverage

### Scenario 2 — Code Generation with Claude Code (Q1–Q10)

| Q | Task statement | Tests |
|---|---|---|
| 1 | 3.1 | Hierarchy diagnosis — user-level config isn't shared |
| 2 | 3.1 | `/memory` as the guide's named diagnostic for inconsistent behavior across sessions |
| 3 | 3.1 | `@import` for modular per-package standards |
| 4 | 3.1 | Splitting a monolithic `CLAUDE.md` into `.claude/rules/` |
| 5 | 3.2 | `context: fork` for verbose skill output |
| 6 | 3.2 | `allowed-tools` — the guide's tool-scoping answer (docs: it grants; `disallowed-tools` restricts) |
| 7 | 3.2 | `argument-hint` — the guide's "required parameters" framing (docs: autocomplete hint only) |
| 8 | 3.2 | Personal skill variants without affecting teammates |
| 9 | 3.4 | Direct execution for well-scoped changes |
| 10 | 3.5 | Concrete input/output examples over prose |

### Scenario 5 — Claude Code for CI (Q11–Q20)

| Q | Task statement | Tests |
|---|---|---|
| 11 | 3.6 | `--output-format json` + `--json-schema` for inline PR comments |
| 12 | 3.6 | Avoiding duplicate comments on re-review |
| 13 | 3.6 | Existing tests in context to prevent duplicate scenarios |
| 14 | 3.6 | `CLAUDE.md` as the CI context mechanism |
| 15 | 4.6 | Independent review instance vs self-review |
| 16 | 4.1 | Explicit categorical criteria over "be conservative" |
| 17 | 4.1 | Temporarily disabling a high-false-positive category |
| 18 | 4.1 | Severity criteria with concrete code examples |
| 19 | 4.2 | Few-shot for output-format consistency |
| 20 | 4.4 | `detected_pattern` for dismissal analysis |

> [!WARNING] This set has its own letter skew — don't pattern-match here either
> Answer distribution across the 20 items is **A=3 · B=10 · C=6 · D=1**. That is less `A`-heavy than the official sample set's 10-of-12, but it is not flat, and `B` carries half the set. Answer by reasoning; a letter is never evidence.

## How to use it

1. Sit Q1–Q20 closed-book, ~30 minutes.
2. Mark against the two keys; every entry rebuts all three distractors.
3. Log misses in [[Weak Areas Deep Dive]] tagged `D3` so they surface alongside your CyberSkill gaps.
4. Anything you miss twice → re-read the mapped `§` in [[D3 - Claude Code Configuration & Workflows]], then the matching episode ([[EP10 - CLAUDE.md Hierarchy & Config Rules]] · [[EP11 - Custom Slash Commands & Skills]] · [[EP12 - Plan Mode vs Execute]] · [[EP13 - Claude Code CI-CD Pipelines]]).

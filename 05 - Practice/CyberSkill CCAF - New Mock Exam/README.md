---
tags:
  - CCA-F
  - practice-exam
date: 2026-08-23
status: done
---

# CCA-F New Mock Exam — Index

A 60-question CCA-F sitting drawn from the [CyberSkill practice bank](https://practice.cyberskill.world/practice/ccaf/practice) on **2026-08-23** (sitting `7039f634`), with a full worked answer key.

## Files

| File | What |
|---|---|
| [Questions.md](Questions.md) | All 60 questions + all four options each. **Correct answers deliberately unmarked** — work through this first. |
| [Answer Key/research_pipeline.md](Answer%20Key/research_pipeline.md) | 15 answers — Q5, 17, 24, 25, 28, 29, 32, 34, 43, 47, 48, 52, 53, 55, 60 |
| [Answer Key/code_exploration.md](Answer%20Key/code_exploration.md) | 15 answers — Q1, 7, 9, 10, 11, 12, 14, 20, 31, 37, 38, 39, 42, 44, 50 |
| [Answer Key/customer_support.md](Answer%20Key/customer_support.md) | 14 answers — Q2, 6, 8, 15, 16, 23, 26, 30, 40, 41, 45, 54, 58, 59 |
| [Answer Key/extraction_pipeline.md](Answer%20Key/extraction_pipeline.md) | 16 answers — Q3, 4, 13, 18, 19, 21, 22, 27, 33, 35, 36, 46, 49, 51, 56, 57 |

Each answer entry gives the **correct answer with its full text**, why it wins, **every wrong option quoted in full** with the reason it fails, and a portable takeaway — so you never have to flip back to the question file.

> [!NOTE] Question numbers are the sitting's own, and they interleave
> Unlike the older `CyberSkill CCAF - Mock Exam` sitting (where Q1–Q15 are all one domain), this sitting's questions arrive shuffled. `Q1` is `code_exploration`, `Q2` is `customer_support`, and so on. The domain files keep the **original** numbers so they line up with [Questions.md](Questions.md) — which means the numbers inside each domain file are not contiguous.

## Verification status — 57/60 grader-confirmed, 0 corrections

The grade endpoint returns the authoritative `correct_key` for every item in a sitting, and item IDs are stable across sittings — so a fresh sitting reveals the true answers for whichever items it redraws.

- **2026-08-23** — a sitting covered **45** of these 60 items. All 45 matched this key.
- **2026-08-24** — the [Timed Mock](../CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/README.md) redrew **12 of the remaining 15** (the nine 📘 items other than Q16/Q55, plus three of the four 🤔). All 12 matched. The pairings are tabulated in that folder.

**No answer in this key has ever needed correcting.** Because the 2026-08-24 sitting was graded before being compared with this folder, the agreement is not circular.

| Mark | Meaning | Count | Questions |
|---|---|---|---|
| ✅ | Confirmed by the site's grader | **57** | **All except Q16, Q45, Q55** |
| 📘 | Matches the doc-verified key in `CyberSkill CCAF - Mock Exam/` only | 2 | Q16, Q55 |
| 🤔 | Reasoned judgment — the last remaining uncertainty | 1 | Q45 |

The **three still-open items** were not redrawn by either later sitting:

| Q | Topic | Status |
|---|---|---|
| Q16 | `process_refund`: transient technical vs permanent business errors | 📘 — matches the older doc-verified key |
| Q55 | Sequential precedent analysis, 3-minute latency | 📘 — matches the older doc-verified key |
| Q45 | Frustrated customer, policy-disallowed refund | 🤔 — reasoned only |

## Answer grid

| Q | A | Q | A | Q | A | Q | A | Q | A | Q | A |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | C | 11 | B | 21 | B | 31 | D | 41 | C | 51 | A |
| 2 | A | 12 | D | 22 | B | 32 | C | 42 | C | 52 | C |
| 3 | B | 13 | C | 23 | B | 33 | D | 43 | A | 53 | B |
| 4 | B | 14 | B | 24 | B | 34 | B | 44 | A | 54 | A |
| 5 | C | 15 | A | 25 | C | 35 | D | 45 | B | 55 | C |
| 6 | C | 16 | A | 26 | B | 36 | B | 46 | B | 56 | A |
| 7 | B | 17 | B | 27 | B | 37 | D | 47 | B | 57 | D |
| 8 | B | 18 | C | 28 | B | 38 | B | 48 | C | 58 | B |
| 9 | B | 19 | D | 29 | D | 39 | D | 49 | D | 59 | B |
| 10 | B | 20 | C | 30 | C | 40 | B | 50 | D | 60 | C |

> [!IMPORTANT] The **middle** of three sittings from the same bank — kept deliberately separate
> Two siblings draw from the **same item bank** as this folder:
>
> | Sibling | Sat | Overlap with this folder | Has questions? |
> |---|---|---|---|
> | [../CyberSkill CCAF - Mock Exam/](../CyberSkill%20CCAF%20-%20Mock%20Exam/README.md) | earlier | **42 / 60** | ❌ answers only |
> | [../CyberSkill CCAF - Timed Mock 2026-08-24/](../CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/README.md) | 2026-08-24 | **48 / 60** | ⚠️ stems only, no options |
>
> All three are **not merged and should not be merged**: each sitting has its own internally consistent `Q1`–`Q60`, and the bank re-draws and re-orders on every sitting. `Q7` here is not `Q7` in either sibling. Expect to meet the same question under three different numbers — that repetition is useful revision, and it is what allowed 57 of these 60 answers to be independently confirmed.
>
> This folder is the only one quoting all four options per item.
>
> Unrelated fourth set: [../CCA-F Question Bank/](../CCA-F%20Question%20Bank/README.md) — a certificationpractice.com bank, **1/60** overlap with this sitting.

## Conventions in this folder

Links **between files in this folder** are relative Markdown (`[text](file.md)`), matching `CyberSkill CCAF - Mock Exam/` — the domain filenames are shared between the two folders, so `[[wikilinks]]` would be ambiguous. Links **out of the folder** use `[[wikilinks]]` as normal. Keep the folder intact as a unit.

## Related notes

- [[Weak Areas Deep Dive]] — log the ones you missed here
- [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]]
- [[Flashcards]] · [[Critical Terms Glossary]]

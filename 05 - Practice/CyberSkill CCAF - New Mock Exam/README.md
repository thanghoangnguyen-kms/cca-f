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

## Verification status — 45/45 exact match

On 2026-08-23 this key was checked against the practice site's own grader. The grade endpoint returns the authoritative `correct_key` for every item in a sitting, and item IDs are stable across sittings — so a fresh sitting reveals the true answers for whichever items it redraws. One sitting covered **45 of these 60 questions, and all 45 matched this key exactly**.

| Mark | Meaning | Count | Questions |
|---|---|---|---|
| ✅ | Confirmed by the site's grader | 45 | Q1, 3–10, 12–15, 17–25, 28–33, 35–43, 47–49, 52, 53, 56, 57, 59 |
| 📘 | Matches the doc-verified key in `CyberSkill CCAF - Mock Exam/` | 11 | Q2, 11, 16, 26, 27, 44, 50, 51, 54, 55, 60 |
| 🤔 | Reasoned judgment — the only real uncertainty | 4 | Q34, Q45, Q46, Q58 |

The guest quota is **1 mock per 7 days**, so the remaining 15 could not be redrawn. That window reopens **2026-08-31**; one more sitting should close most of the gap.

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

> [!IMPORTANT] The **newer** of two sittings from the same bank — kept deliberately separate
> The sibling [../CyberSkill CCAF - Mock Exam/](../CyberSkill%20CCAF%20-%20Mock%20Exam/README.md) is an earlier sitting of the **same item bank** — measured overlap is **40 of 60 questions**. Its question file was never captured, so it is answers only.
>
> The two are **not merged and should not be merged**: each sitting has its own internally consistent `Q1`–`Q60`, and the bank re-draws and re-orders on every sitting. `Q7` here is not `Q7` there. Expect to meet the same question twice under two different numbers.
>
> Unrelated third set: [../CCA-F Question Bank/](../CCA-F%20Question%20Bank/README.md) — a certificationpractice.com bank, **1/60** overlap with this sitting.

## Conventions in this folder

Links **between files in this folder** are relative Markdown (`[text](file.md)`), matching `CyberSkill CCAF - Mock Exam/` — the domain filenames are shared between the two folders, so `[[wikilinks]]` would be ambiguous. Links **out of the folder** use `[[wikilinks]]` as normal. Keep the folder intact as a unit.

## Related notes

- [[Weak Areas Deep Dive]] — log the ones you missed here
- [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]]
- [[Flashcards]] · [[Critical Terms Glossary]]

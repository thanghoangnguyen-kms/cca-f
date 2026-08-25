---
tags:
  - CCA-F
  - practice-exam
date: 2026-08-24
status: done
---

# CCAF Timed Mock 2026-08-24 — Index

A 60-question **timed** CCA-F mock from the [CyberSkill practice bank](https://practice.cyberskill.world/practice/ccaf/practice), sat **2026-08-24 15:36** (sitting `7a03a635`, taken signed in). This is the **third** sitting of the same CyberSkill item bank held in the vault.

## Result

| | |
|---|---|
| **Score** | **71.67% — 43/60** |
| Pass mark | 72% — **CyberSkill's own bar, not the exam's** (see [[Official Exam Blueprint]] § 1) |
| Outcome | ❌ **Failed by one question.** 44/60 = 73.3% would have passed |

> [!NOTE] One mark. That is the whole story of this sitting.
> 72% of 60 is 43.2, so 44 correct is this site's pass. You scored 43. **The real exam does not work this way** — it scores 720 on a scaled 100–1,000 range, criterion-referenced and equated across forms, so no raw percentage maps cleanly onto it. Treat 72% as a practice target, not a prediction. There is no need for a broad strategy change — six of the seventeen misses are in `customer_support`, and four of those are questions you can answer from rules you demonstrably already know (see the cross-references in each key). Fix that domain and this becomes a comfortable pass.

## Domain breakdown

| Domain | Score | Answer key |
|---|---|---|
| `code_exploration` | **15/16 (94%)** — strongest | [Answer Key/code_exploration.md](Answer%20Key/code_exploration.md) |
| `extraction_pipeline` | 10/15 (67%) | [Answer Key/extraction_pipeline.md](Answer%20Key/extraction_pipeline.md) |
| `research_pipeline` | 10/15 (67%) | [Answer Key/research_pipeline.md](Answer%20Key/research_pipeline.md) |
| `customer_support` | **8/14 (57%)** — weakest | [Answer Key/customer_support.md](Answer%20Key/customer_support.md) |

## Files

| File | What |
|---|---|
| [Questions.md](Questions.md) | All 60 stems in sitting order, **unmarked**. Stems only — no A–D options (see Fidelity below) |
| [Answer Key/customer_support.md](Answer%20Key/customer_support.md) | 14 answers — Q1, 3, 16, 18, 19, 20, 23, 26, 33, 34, 36, 38, 41, 60 |
| [Answer Key/extraction_pipeline.md](Answer%20Key/extraction_pipeline.md) | 15 answers — Q2, 5, 6, 7, 10, 12, 13, 14, 17, 24, 25, 27, 31, 42, 59 |
| [Answer Key/code_exploration.md](Answer%20Key/code_exploration.md) | 16 answers — Q4, 9, 11, 22, 28, 29, 37, 39, 40, 43, 45, 52, 55, 56, 57, 58 |
| [Answer Key/research_pipeline.md](Answer%20Key/research_pipeline.md) | 15 answers — Q8, 15, 21, 30, 32, 35, 44, 46, 47, 48, 49, 50, 51, 53, 54 |

Question numbers are this sitting's own. Domains **interleave** (Q1 is `customer_support`, Q2 is `extraction_pipeline`…), so the numbers inside each domain file are not contiguous — they stay original so they line up with [Questions.md](Questions.md).

## The 17 misses

| Q | Domain | You | Correct | One-line rule |
|---|---|---|---|---|
| 13 | extraction | C | **A** | Two derivable values disagree → emit both, flag the delta |
| 14 | extraction | C | **D** | Info present but varied in format → few-shot examples |
| 18 | support | B | **C** | The model reasons over tool results; the loop has no decision tree |
| 19 | support | A | **B** | Regulated advice is out of scope — name the limit, route on |
| 21 | research | B | **C** | Don't normalize away differences that carry information |
| 23 | support | D | **A** | Scope the problem before calling tools |
| 27 | extraction | B | **C** | Batch cadence = arrival wait + window + retry headroom ≤ SLA |
| 38 | support | B | **A** | Judgment criteria beat counters for semantic conditions |
| 40 | code | B | **C** | Context exhaustion → summarize + fresh context, not better prompts |
| 41 | support | A | **C** | In-session context pressure → compact inactive spans |
| 42 | extraction | C | **A** | Scarce review capacity → calibrated confidence ranking |
| 47 | research | D | **A** | Fix the output contract upstream, not the synthesizer |
| 49 | research | C | **A** | Provenance is a structured field, never inline prose |
| 50 | research | C | **D** | Delegate goals + quality criteria, never procedures |
| 54 | research | B | **C** | Render each content type on its own terms |
| 59 | extraction | C | **B** | Keep the enum closed; add `"other"` + a detail field |
| 60 | support | D | **B** | `isError: true` is the error channel — enrich it, don't replace it |

> [!TIP] Three misses repeat a rule you applied correctly elsewhere *in this same sitting*
> - **Q49** vs **Q15** — same failure (citations lost in synthesis); you chose structured mappings correctly at Q15, then inline prose citations at Q49.
> - **Q60** vs **Q20** — you correctly enriched errors with `errorCategory`/`isRetryable` at Q20, then abandoned `isError` at Q60.
> - **Q40** vs **Q11** and **Q58** — you handled context exhaustion correctly twice, then chose "prompt more specifically" at Q40.
>
> These are not knowledge gaps. They are recognition failures under time pressure — which is what a *timed* mock is for surfacing. Drill the trigger, not the content.

## Answer grid

| Q | A | Q | A | Q | A | Q | A | Q | A | Q | A |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | B | 11 | B | 21 | C | 31 | D | 41 | C | 51 | C |
| 2 | D | 12 | B | 22 | D | 32 | C | 42 | A | 52 | B |
| 3 | B | 13 | A | 23 | A | 33 | A | 43 | B | 53 | B |
| 4 | B | 14 | D | 24 | B | 34 | B | 44 | B | 54 | C |
| 5 | D | 15 | B | 25 | B | 35 | B | 45 | D | 55 | C |
| 6 | C | 16 | B | 26 | C | 36 | B | 46 | A | 56 | B |
| 7 | D | 17 | B | 27 | C | 37 | D | 47 | A | 57 | B |
| 8 | C | 18 | C | 28 | B | 38 | A | 48 | C | 58 | A |
| 9 | D | 19 | B | 29 | B | 39 | C | 49 | A | 59 | B |
| 10 | B | 20 | A | 30 | C | 40 | C | 50 | D | 60 | B |

## Verification status — 60/60 grader-authoritative

Unlike the other two sittings, **nothing here was reasoned or inferred**. Every answer in this folder is the site's own `correct_key`, read straight off the review page: for the 17 misses the page states the correct option explicitly, and for the 43 correct items the recorded selection *is* the correct option. There are no 🤔 items and no doc-derived inferences.

Two items were re-checked against official Anthropic docs on 2026-08-24 rather than accepted as written:

- **Q8's keyed option uses `allowedTools`**, which is wrong — the `AgentDefinition` inner field is `tools`. More importantly, the *failure mode* is the other one: omitting `Agent` from `allowedTools` surfaces (it routes to `canUseTool`, or is denied), whereas a tool omitted from `AgentDefinition.tools` is absent from the session entirely — no prompt, no error. The stem's "logs show no errors" fits the second. The option is still the intended answer. See [Answer Key/research_pipeline.md](Answer%20Key/research_pipeline.md).
- **Q22's name-based `--resume`** was initially flagged unverified. It has since been **confirmed**: `--resume` takes a session ID *or* name, and the CLI reference's own example is `claude --resume auth-refactor`. The warning has been removed. Seven existing vault notes already stated this correctly — the flag was the error, not the notes. See [Answer Key/code_exploration.md](Answer%20Key/code_exploration.md).

## What this sitting confirmed elsewhere

Because this sitting draws from the same bank as `New Mock Exam`, its grader-authoritative answers resolve 12 items that folder could not previously confirm. All 12 agreed; none needed correcting. That took its key from **45/60 to 57/60**.

New Mock Exam item → the item here that confirmed it:

| Was | This folder | Answer | Confirmed as |
|---|---|---|---|
| 🤔 | Q34 — non-converging research run | **B** | Q35 |
| 🤔 | Q46 — schema field absent from source | **B** | Q24 |
| 🤔 | Q58 — request for specific legal advice | **B** | Q19 |
| 📘 | Q2 — escalation trigger design | **A** | Q38 |
| 📘 | Q11 — untested paths across 45 files | **B** | Q11 |
| 📘 | Q26 — customer returns after 4 hours | **B** | Q34 |
| 📘 | Q27 — invented `attendee_count` | **B** | Q25 |
| 📘 | Q44 — inconsistency in 30+ minute sessions | **A** | Q58 |
| 📘 | Q50 — interrupted subagent, renamed functions | **D** | Q45 |
| 📘 | Q51 — 12% semantic errors, 20% review capacity | **A** | Q42 |
| 📘 | Q54 — `lookup_order` returning 40+ fields | **A** | Q33 |
| 📘 | Q60 — information flow between two subagents | **C** | Q32 |


A further **ten** items absent from `New Mock Exam` turn up in the `Mock Exam` key — Q8, Q13, Q16, Q22, Q23, Q41, Q46, Q49, Q51, Q56 — and **all ten agree with its answers**, which is ten independent confirmations of that older doc-verified key.

## Fidelity — what this folder does *not* have

> [!WARNING] Stems and correct answers only. No distractors.
> The review page renders **your selected option and the correct option, and nothing else**. The two unchosen options per item are never sent to the browser; the page is a server-rendered component with no client-side item payload, and no `/api/...` endpoint returns the full item. So:
>
> | | New Mock Exam | Mock Exam | **This sitting** |
> |---|---|---|---|
> | Full A–D options | ✅ quoted in full | ⚠️ bare `A:`/`B:` refs | ❌ **not captured** |
> | Correct answers | **57/60** grader-confirmed | doc-verified | ✅ **60/60 grader-confirmed** |
> | Per-distractor rebuttals | ✅ | ✅ | ❌ not possible here — but available for 52 of the 60 items via the two sibling folders |
>
> **The trade is fidelity of the question for certainty of the answer.** Use this folder as the authoritative key and as an **open-response** drill; use `CyberSkill CCAF - New Mock Exam/` when you want multiple choice.

## Overlap with the other sets

| Pairing | Overlap | How measured |
|---|---|---|
| This sitting ↔ **New Mock Exam** | **48 / 60** | Distinctive phrase from each of the 60 stems, matched against that folder's `Questions.md` |
| This sitting ↔ **Mock Exam** | **42 / 60** | Mapped all 60 stems against that folder's `**Question:**` paraphrases. The 18 non-matches are exactly this sitting's short-form items — that sitting drew none |
| This sitting ↔ **Question Bank** | **0 / 60** | No shared item. Q6 and one Question Bank item both name `extract_metadata`, but Q6 tests tool-call ordering and that item tests a `Message Batches API` limit — a coincidence of naming, not an overlap |

**12 items are new relative to `New Mock Exam`:** Q4, Q8, Q10, Q13, Q16, Q22, Q23, Q41, Q46, Q49, Q51, Q56. But **ten of those twelve** — Q8, Q13, Q16, Q22, Q23, Q41, Q46, Q49, Q51, Q56 — appear in the `Mock Exam` key, and **all ten agree with its answers.** So only **two items are new to the vault outright: Q4 and Q10**, and both were answered correctly.

> [!IMPORTANT] Those ten agreements are worth more than the novelty count
> They are ten further independent confirmations of the older `Mock Exam` key, which was verified against docs rather than against a grader. Nothing else in the vault records them.

**Novelty did not drive the misses — the opposite.** Q13, Q41 and Q49 were each already worked in full, with distractor rebuttals, in the `Mock Exam` key. Missing them is therefore a *worse* result than missing something unseen, not a novelty artefact. The real cluster is by domain (`customer_support`, 6 of 17) and by trap type.

> [!IMPORTANT] Kept separate from the other two sittings — same rule as before
> One item bank, three sittings, each re-drawn and re-ordered. `Q7` here is not `Q7` in either sibling folder. **Do not merge and never carry a question number between sets.** Meeting the same item under three numbers is useful revision.

## Conventions in this folder

Links **between files in this folder** are relative Markdown (`[text](file.md)`), matching the two sibling CyberSkill folders — the four domain filenames are now shared across *three* folders, so `[[wikilinks]]` would be ambiguous. Links **out of the folder** use `[[wikilinks]]`. Keep the folder intact as a unit.

## Related notes

- [[Weak Areas Deep Dive]] — log these 17 misses there
- [[Answer Patterns Index]] — look each miss up and drill its whole pattern
- [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]]
- [[Flashcards]] · [[Critical Terms Glossary]]
- Sibling sittings: [../CyberSkill CCAF - New Mock Exam/](../CyberSkill%20CCAF%20-%20New%20Mock%20Exam/README.md) · [../CyberSkill CCAF - Mock Exam/](../CyberSkill%20CCAF%20-%20Mock%20Exam/README.md)

**Back to:** [[00 - START HERE]] · [../README.md](../README.md)

---
tags:
  - CCA-F
  - practice
date: 2026-08-24
status: in-progress
---

# 05 - Practice — Index

Six exam sets — the **official** sample questions, three sittings of one CyberSkill bank, an unrelated bank, and a vault-authored set closing the Claude Code gap — plus your personal mistake log.

> [!IMPORTANT] The organizing rule
> **One folder per exam set, named `<source> - <set>`; standalone notes at the root.** Every set folder owns a `README.md`. Links *within* a set folder are relative Markdown so the folder stays portable as a unit; everything else in the vault uses `[[wikilinks]]`.

## Start here

| Folder | Source | Why first |
|---|---|---|
| [Exam Guide - Sample Questions/](Exam%20Guide%20-%20Sample%20Questions/README.md) | **Official exam guide § 9** | ✅ 12 Qs, all four options, **Anthropic-authored rationales that rebut every distractor**. The only officially-sourced items in the vault — and the highest-authority key. Do these before anything else |
| [Vault-authored - Claude Code Scenario Drills/](Vault-authored%20-%20Claude%20Code%20Scenario%20Drills/README.md) | Written from the official task statements | ⚠️ 20 Qs covering **official scenarios 2 and 5** — the two no sourced bank tests. Topics are exam-accurate; difficulty calibration is not. See its § Provenance |

## The four third-party sets

| Folder | Source | Questions | Answer key |
|---|---|---|---|
| [CyberSkill CCAF - New Mock Exam/](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/README.md) | CyberSkill CCAF, sitting 2026-08-23 | ✅ 60, unmarked, **all 4 options each** | ✅ worked, 4 domains · **57/60 grader-confirmed** |
| [CyberSkill CCAF - Mock Exam/](CyberSkill%20CCAF%20-%20Mock%20Exam/README.md) | CyberSkill CCAF, earlier sitting | ❌ never captured | ✅ worked, 4 domains |
| [CyberSkill CCAF - Timed Mock 2026-08-24/](CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/README.md) | CyberSkill CCAF, **timed** sitting 2026-08-24 | ⚠️ 60 stems, **no options** | ✅ **60/60 grader-authoritative** |
| [CCA-F Question Bank/](CCA-F%20Question%20Bank/README.md) | certificationpractice.com #2564 | ✅ 60, unmarked, **all 4 options each** | ⚠️ [worked, one file](CCA-F%20Question%20Bank/Answer%20Key.md) · **vault-reasoned, no grader** |

> [!TIP] Which folder to reach for
> **Drilling as multiple choice** → `New Mock Exam` or `CCA-F Question Bank` (the two sets quoting all four options). **Settling what the right answer is** → `Timed Mock 2026-08-24` (every answer is the site's own `correct_key`). **Your own scored performance** → `Timed Mock 2026-08-24`; it is the only sitting whose result is recorded.

Plus two standalone notes at the root:

- [[Weak Areas Deep Dive]] — your mistake log. Start and end there.
- [[Answer Patterns Index]] — all three CyberSkill keys read *sideways*: 180 explanations grouped into recurring principles, each tied to a trigger row in [[00-golden-rules-cheatsheet]], with drill sets and the rules no sitting tests. The 2026-08-24 items drill as open-response only.

## How they relate

Measured question overlap, every pairing:

| Pairing | Overlap |
|---|---|
| New Mock Exam ↔ Mock Exam | **40 / 60** — same CyberSkill bank |
| New Mock Exam ↔ Timed Mock 2026-08-24 | **48 / 60** — same CyberSkill bank |
| Mock Exam ↔ Timed Mock 2026-08-24 | **42 / 60** — measured by mapping all 60 stems against that folder's `**Question:**` paraphrases; the 18 non-matches are exactly the Timed Mock's short-form items |
| Question Bank ↔ Mock Exam | 0 / 60 |
| Question Bank ↔ New Mock Exam | 1 / 60 (a single coincidental topic match) |
| Question Bank ↔ Timed Mock 2026-08-24 | 0 / 60 — one shared tool name (`extract_metadata`), two unrelated questions |

**Only two items appear nowhere else in the vault:** the 2026-08-24 sitting's Q4 and Q10. Ten of its twelve items absent from `New Mock Exam` turn up in the `Mock Exam` key instead — and **all ten agree with that key's answers**, which is ten unrecorded independent confirmations of it.

> [!IMPORTANT] The three CyberSkill sittings stay separate — by design
> They draw from one item bank, so ~⅔ of their questions coincide, but each sitting has its own internally consistent `Q1`–`Q60` and the bank re-draws and re-orders every time. **Do not merge them** — it would break three numberings for no gain. Meeting the same question under three numbers is useful revision, and it is exactly what let 57 of the `New Mock Exam` answers be independently confirmed.
>
> And **never carry a question number between sets.** All four of *those* number `Q1`–`Q60`, and none of the numberings correspond. (The two sets in *Start here* number `Q1`–`Q12` and `Q1`–`Q20`.)

## The three domain-split answer keys

All three use the same four scenario domains, and all three use the same four filenames — `research_pipeline.md`, `code_exploration.md`, `customer_support.md`, `extraction_pipeline.md`. That collision — now three-way — is why these folders link internally with relative Markdown: a bare `[[code_exploration]]` would be ambiguous. Reach them through their folder's README.

| | New Mock Exam | Mock Exam | Timed Mock 2026-08-24 |
|---|---|---|---|
| Numbering | Sitting order — domains **interleave** (Q1 code, Q2 support…) | Blocked — Q1–15 research, Q16–30 code, Q31–45 support, Q46–60 extraction | Sitting order — domains **interleave** (Q1 support, Q2 extraction…) |
| Domain split | research 15 · code 15 · support 14 · extraction 16 | research 15 · code 15 · support 15 · extraction 15 | research 15 · code 16 · support 14 · extraction 15 |
| Question file | [Questions.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Questions.md), same folder | Not in the vault | [Questions.md](CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/Questions.md) — **stems only** |
| Wrong options | Quoted in full in each entry | Referenced as bare `A:`/`B:` — lower fidelity | ❌ **not recoverable** — the site never sends them |
| Verification | **57/60** confirmed against the site's grader | Verified against official Anthropic docs | **60/60** — the grader's own `correct_key` |
| Your score recorded | ❌ | ❌ | ✅ **43/60 (71.67%)** |

## Gaps worth filling

- ~~**`CCA-F Question Bank/` has no answer key**~~ — **closed 2026-08-25.** All 60 are now worked in [CCA-F Question Bank/Answer Key.md](CCA-F%20Question%20Bank/Answer%20Key.md). Caveat: the source publishes no answers, so that key is **reasoned from the blueprint and official docs, not confirmed by a grader** — the only key here with no external check. It flags five items (Q14, Q16, Q21, Q51, Q55) where the bank's own wording is technically wrong. All 60 answers were re-verified 2026-08-25 in four independent adversarial passes against official docs — **no keyed letter changed**.
- **None of the three CyberSkill sittings tests most of D3 / Claude Code ops** — no `CLAUDE.md` hierarchy, plan mode, `.mcp.json` scoping, or `claude -p` in CI, and no `stop_reason` item. ⚠️ **Now partly covered** by [Vault-authored - Claude Code Scenario Drills/](Vault-authored%20-%20Claude%20Code%20Scenario%20Drills/README.md) — but with vault-authored questions, not sourced ones. Verified at full fidelity across the 60 `New Mock Exam` items and at stem/paraphrase fidelity across the other 120. **Hooks are the exception and *are* tested** — a hook is the keyed answer on the compliance/$500 item in two of the three sittings. Session lifecycle (`--resume` vs `--continue`, `fork_session`) and the built-in tools are covered too. A partial blind spot in this source, not in the exam; see [[Answer Patterns Index]] § Part 1. ⚠️ **Also now partly covered by sourced questions** — 13 of the 60 items (≈22%) in [CCA-F Question Bank/](CCA-F%20Question%20Bank/README.md) are Claude Code operations ( `CLAUDE.md` splitting, `.claude/rules/` with `paths:`, project vs user scope for commands, plan mode vs direct execution, `--resume`, `Glob`/`Edit` fallbacks, and `claude -p` in CI), with worked answers.
- **The 2026-08-24 sitting has no distractors** — 60 authoritative answers, but the site's review page never sends the unchosen options, so that folder cannot be drilled as multiple choice. In practice this bites on only **two** items: Q4 and Q10, the only ones with no sibling entry. The other 58 have full option lists or worked distractor rebuttals in `New Mock Exam` or `Mock Exam`.
- **New Mock Exam's unverified answers: closed from 15 to 3.** The 2026-08-24 sitting redrew 12 of the 15 and every one matched, so that key now stands at 57/60 grader-confirmed. Still open: its Q16 and Q55 (📘 doc-verified only) and Q45 (🤔 reasoned only).

> [!IMPORTANT] What the official blueprint says about this gap
> The exam draws **4 scenarios from a bank of 6**, and two of the six — *Code Generation with Claude Code* and *Claude Code for Continuous Integration* — are exactly the D3 material these banks skip. **P(at least one appears) ≈ 93%.** See [[Official Exam Blueprint]] § 3.

## Suggested loop

0. **Sit the [official sample questions](Exam%20Guide%20-%20Sample%20Questions/README.md) first** — twelve items, but they calibrate you to the real house style before third-party phrasing sets bad habits.
1. Work a set from its questions file **without** the key open.
2. Grade against the domain answer files.
3. Log every miss in [[Weak Areas Deep Dive]] — capture *why* the right answer is right.
4. Look the miss up in [[Answer Patterns Index]] and drill its whole pattern — the other 4–10 questions testing the same rule, across both sittings.
5. Follow the takeaways back into [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]].

6. Where a CyberSkill key disagrees with the [official key](Exam%20Guide%20-%20Sample%20Questions/Answer%20Key.md), **the official key wins** — and that disagreement belongs in [[Weak Areas Deep Dive]].

**Back to:** [[00 - START HERE]] · [[CCA-F Study Roadmap]] · [[Official Exam Blueprint]]

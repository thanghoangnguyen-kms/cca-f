---
tags:
  - CCA-F
  - practice
date: 2026-08-24
status: in-progress
---

# 05 - Practice — Index

Three independent exam sets plus your personal mistake log.

> [!IMPORTANT] The organizing rule
> **One folder per exam set, named `<source> - <set>`; standalone notes at the root.** Every set folder owns a `README.md`. Links *within* a set folder are relative Markdown so the folder stays portable as a unit; everything else in the vault uses `[[wikilinks]]`.

## The three sets

| Folder | Source | Questions | Answer key |
|---|---|---|---|
| [CyberSkill CCAF - New Mock Exam/](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/README.md) | CyberSkill CCAF, sitting 2026-08-23 | ✅ 60, unmarked | ✅ worked, 4 domains |
| [CyberSkill CCAF - Mock Exam/](CyberSkill%20CCAF%20-%20Mock%20Exam/README.md) | CyberSkill CCAF, earlier sitting | ❌ never captured | ✅ worked, 4 domains |
| [CCA-F Question Bank/](CCA-F%20Question%20Bank/README.md) | certificationpractice.com #2564 | ✅ 60, unmarked | ❌ **none yet** |

Plus two standalone notes at the root:

- [[Weak Areas Deep Dive]] — your mistake log. Start and end there.
- [[Answer Patterns Index]] — the two CyberSkill keys read *sideways*: 120 explanations grouped into 22 recurring principles, each tied to a trigger row in [[00-golden-rules-cheatsheet]], with drill sets and the four rules neither sitting tests.

## How they relate

Measured question overlap, every pairing:

| Pairing | Overlap |
|---|---|
| New Mock Exam ↔ Mock Exam | **40 / 60** — two sittings of the **same** CyberSkill bank |
| Question Bank ↔ Mock Exam | 0 / 60 |
| Question Bank ↔ New Mock Exam | 1 / 60 (a single coincidental topic match) |

> [!IMPORTANT] The two CyberSkill sittings stay separate — by design
> They draw from one item bank, so ~⅔ of their questions coincide, but each sitting has its own internally consistent `Q1`–`Q60` and the bank re-draws and re-orders every time. **Do not merge them** — it would break both numberings for no gain. Meeting the same question twice under two numbers is useful revision.
>
> And **never carry a question number between sets.** All three number `Q1`–`Q60`, and none of the numberings correspond.

## The two domain-split answer keys

Both use the same four scenario domains, and both use the same four filenames — `research_pipeline.md`, `code_exploration.md`, `customer_support.md`, `extraction_pipeline.md`. That collision is why these folders link internally with relative Markdown: a bare `[[code_exploration]]` would be ambiguous. Reach them through their folder's README.

| | New Mock Exam | Mock Exam |
|---|---|---|
| Numbering | Sitting order — domains **interleave** (Q1 code, Q2 support…) | Blocked — Q1–15 research, Q16–30 code, Q31–45 support, Q46–60 extraction |
| Question file | [Questions.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Questions.md), same folder | Not in the vault |
| Wrong options | Quoted in full in each entry | Referenced as bare `A:`/`B:` — lower fidelity |
| Verification | 45/60 confirmed against the site's grader | Verified against official Anthropic docs |

## Gaps worth filling

- **`CCA-F Question Bank/` has no answer key** — 60 questions, nothing worked. The largest gap here.
- **Neither CyberSkill sitting tests D3 / Claude Code ops** — no `CLAUDE.md` hierarchy, hooks, plan mode, `.mcp.json` scoping, or `claude -p` in CI, and no `stop_reason` item either. A blind spot in this source, not in the exam; see [[Answer Patterns Index]] § Part 1.
- **New Mock Exam has 15 unverified answers** — the grader quota reopens **2026-08-31**; one more sitting should close most of it.

## Suggested loop

1. Work a set from its questions file **without** the key open.
2. Grade against the domain answer files.
3. Log every miss in [[Weak Areas Deep Dive]] — capture *why* the right answer is right.
4. Look the miss up in [[Answer Patterns Index]] and drill its whole pattern — the other 4–10 questions testing the same rule, across both sittings.
5. Follow the takeaways back into [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]].

**Back to:** [[00 - START HERE]] · [[CCA-F Study Roadmap]]

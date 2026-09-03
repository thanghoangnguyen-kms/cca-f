---
tags:
  - CCA-F
  - scenarios
date: 2026-09-03
status: done
---

# Scenarios — the layer between concepts and answers

The exam serves **4 blocks of 15 questions, each block built on one production
scenario, drawn at random from a bank of 6** ([[Official Exam Blueprint]] § 3).
Your domain notes are organised by *mechanism*; the exam is organised by
*situation*. These six notes are the bridge.

Each one works the same way, and it is the interrogation from
[[07-anti-patterns-catalog]] § Part 1 applied ahead of time:

1. **What the scenario states** — verbatim from the blueprint.
2. **Requirements → what actually matters** — including the qualifiers that
   silently eliminate whole categories of option.
3. **Requirement → mechanism → layer** — what satisfies each requirement, and
   why at that layer rather than another.
4. **Trade-offs** — what every mechanism costs. There is no free option.
5. **Attractive but wrong** — the distractors this scenario specifically
   attracts, each tagged with its family (`DF1`–`DF7`).

## The six

| # | Scenario | Primary domains | Note |
|---|---|---|---|
| 1 | Customer Support Resolution Agent | D1, D2, D5 | [[Scenario 1 - Customer Support Resolution Agent]] |
| 2 | Code Generation with Claude Code | D3, D5 | [[Scenario 2 - Code Generation with Claude Code]] |
| 3 | Multi-Agent Research System | D1, D2, D5 | [[Scenario 3 - Multi-Agent Research System]] |
| 4 | Developer Productivity with Claude | D2, D3, D1 | [[Scenario 4 - Developer Productivity with Claude]] |
| 5 | Claude Code for Continuous Integration | D3, D4 | [[Scenario 5 - Claude Code for Continuous Integration]] |
| 6 | Structured Data Extraction | D4, D5 | [[Scenario 6 - Structured Data Extraction]] |

> [!TIP] Which to study hardest
> With 4 of 6 drawn at random, each scenario has a **2/3** chance of appearing.
> Scenarios **2** and **5** are the two no third-party bank tests — the official
> guide supplies just three questions each. They are simultaneously your thinnest
> drill volume and, per passer reports, where D3 points leak. Weight accordingly.

> [!WARNING] These notes do not replace the domain notes
> They assume the mechanisms and only decide *between* them. If a mechanism here
> is unfamiliar, follow the link into [[D1 - Agentic Architecture & Orchestration]] ·
> [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] ·
> [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]]
> and learn it there first.

**Back to:** [[00 - START HERE]] · [[Official Exam Blueprint]] · [[07-anti-patterns-catalog]]

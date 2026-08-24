---
tags:
  - CCA-F
  - practice
  - weak-areas
date: 2026-07-11
status: in-progress
---

# 🎯 Weak Areas Deep Dive

> [!NOTE] How to use this note
> This is your **personal mistake log** — fill it in as you work through practice questions (CertSafari, Preporato, the FlorianBruniaux quiz). For each miss, capture *why* the right answer is right, not just what it was. Re-reading this note is the highest-leverage revision you can do before the exam.

**Back to:** [[CCA-F Study Roadmap]] · [[00 - START HERE]]

---

## 📥 Practice sources

**In this vault** — see [README.md](README.md) for the full map:
- **CyberSkill CCAF - New Mock Exam/** — 60 questions **plus** a matched worked key split by domain. The only complete question+answer pair here; start with this one.
- **CyberSkill CCAF - Mock Exam/** — worked answers only (its question file was never captured), same four domains. A **second sitting of the same bank** as New Mock Exam: 40 of 60 questions coincide, under different numbers.
- [[CCA-F-practice-exam-questions]] — a third bank (certificationpractice.com #2564), 60 questions, **no answer key yet**.

> [!WARNING] All three number their questions Q1–Q60 and none of the numberings match. Never cross-reference a question by number across sets.

**External:**
- **CertSafari** — 614 questions: https://www.certsafari.com/anthropic/claude-certified-architect
- **Preporato** practice tests: https://preporato.com/exams/cca-f
- **FlorianBruniaux** 271-question quiz: https://github.com/FlorianBruniaux/claude-code-ultimate-guide/blob/main/quiz

---

## ✍️ Mistake log (template)

Copy this block for each missed question.

> [!EXAMPLE] Missed question
> **Domain:** D_ · **Date:** YYYY-MM-DD
> **Question (paraphrased):** …
> **My answer:** … ❌
> **Correct answer:** … ✅
> **Why I was wrong / the key distinction:** …
> **Rule to remember:** …
> **Links:** [[D1 - Agentic Architecture & Orchestration]]

---

## 📊 Weak-area tally

Track which subdomains keep tripping you up. Add a tally mark each time you miss one; the highest counts are what to re-study.

| Domain | Subdomain | Misses | Notes |
|--------|-----------|:------:|-------|
| D1 | Agent loop / `stop_reason` |  |  |
| D1 | Multi-agent / subagents |  |  |
| D1 | Hooks vs prompts |  |  |
| D2 | Tool interface design |  |  |
| D2 | MCP error responses (`isError`) |  |  |
| D2 | `.mcp.json` vs `~/.claude.json` scoping |  |  |
| D3 | `CLAUDE.md` hierarchy / `@import` |  |  |
| D3 | Plan mode vs direct execution |  |  |
| D3 | CI/CD (`--print`, `--output-format json`) |  |  |
| D4 | JSON schema / `output_config.format` |  |  |
| D4 | Batch vs sync (`custom_id`) |  |  |
| D4 | Validation & retry loops |  |  |
| D5 | Context degradation / `/compact` |  |  |
| D5 | Escalation triggers |  |  |
| D5 | Provenance / source attribution |  |  |

---

## 🔁 Recurring traps I keep falling for

> [!NOTE] Pre-filled with candidates — confirm them against your own misses
> These are the **tier-1 patterns** from [[Answer Patterns Index]], each restated as *the wrong answer that looks right*. They are **not yet your traps** — they're the ones this bank punishes most often, so they're the likeliest candidates.
>
> Tally a mark each time you actually fall for one. **Delete the rows you never miss** — a trap log only works if every row on it is true about you. Add your own rows freely; the bank's frequency is not your weakness profile.

| ❌ The pull I need to resist | ✅ The correct instinct | Tally | Drill |
|---|---|:--:|---|
| Keeping sources **in the prose**, or reconstructing citations at the synthesis step | Claim → source as a **structured, mergeable field** from the very first hop. If rewording can lose it, it will be lost | | Set 1 |
| Picking the trigger that sounds **engineered and measurable** — 3 failed tool calls, a sentiment threshold | Escalate on **reasons**: asked for a human, needs authority the agent lacks, no longer making progress | | Set 2 |
| Choosing the answer that yields a **complete record** — filling the field, picking the closest enum | **Null is an answer.** Partial-with-honest-gaps beats complete-with-invented-values; give closed enums an `"other"` + detail field | | Set 3 |
| Picking the **thorough-sounding** option — read all the files, build an index up front | **Orient → narrow → read.** Structure first, grep second, full reads last. Abstraction before instances | | Set 4 |
| Accepting a design where **workers hand results to each other**, or fan-out nests | Everything flows **through the coordinator's prompts**. Flat fan-out. An arrow between two workers means the wrong diagram | | Set 5 |
| Reaching for a **bigger context window** or "read more carefully" when answers go generic | **Compact, then isolate.** Summarize before you delegate; a scratchpad is durable memory, a bigger window is a slower leak | | Set 6 |
| Mixing up `--continue` / `--resume`, or **starting fresh** when context is still good | `--resume <id>` targets a *specific* session · `fork_session` for mutually-exclusive branches · files changed → resume **and name the delta** | | Set 7 |
| Reaching for **model tier, temperature, or "more examples"** on inconsistent output | "Inconsistent output" is a **missing specification**. Show the canonical shape with *diverse* examples — variety, not volume | | Set 8 |
| _(mine)_ Picking a **prompt-based** answer when the question says "guaranteed" / "compliance" / "cannot rely on the model" | That is **always** code — hook, gate, validator. Prompts move probability; code fixes outcome | | [[00-golden-rules-cheatsheet]] |

Drill-set numbers refer to [[Answer Patterns Index]] § Part 3 — each set is 3–6 questions across both CyberSkill sittings that test the same rule. Work a whole set in one go, then write the rule from memory *before* opening the key.

> [!TIP] The faster tell is the **failure mode**, not the topic
> Nearly every wrong option in this bank fails in one of four ways: a **proxy** standing in for the real signal · a **prompt where code belongs** (or a rule table where judgment belongs) · **information thrown away and then reconstructed** · **complexity that doesn't pay**. Naming the failure mode is quicker than recalling 120 answers. Full catalogue: [[07-anti-patterns-catalog]].

*Related cram material: [[Flashcards]] · [[Critical Terms Glossary]] · Handbook anti-patterns in `04 - Exam Cram/Handbook/`.*

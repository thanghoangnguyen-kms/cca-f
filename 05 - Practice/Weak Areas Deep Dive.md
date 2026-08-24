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

> [!WARNING] Add your own patterns here
> - _e.g._ "I keep picking a prompt-based answer when the question says 'guaranteed' — that's always a **code/hook** answer."

*Related cram material: [[Flashcards]] · [[Critical Terms Glossary]] · Handbook anti-patterns in `04 - Exam Cram/Handbook/`.*

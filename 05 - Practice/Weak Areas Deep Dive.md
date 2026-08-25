---
tags:
  - CCA-F
  - practice
  - weak-areas
date: 2026-08-24
status: in-progress
---

# 🎯 Weak Areas Deep Dive

> [!NOTE] How to use this note
> This is your **personal mistake log** — fill it in as you work through practice questions, starting with the vault's **official** set ([Exam Guide - Sample Questions](Exam%20Guide%20-%20Sample%20Questions/README.md)) and then the third-party banks (CertSafari, Preporato, the FlorianBruniaux quiz). For each miss, capture *why* the right answer is right, not just what it was. Re-reading this note is the highest-leverage revision you can do before the exam.

**Back to:** [[CCA-F Study Roadmap]] · [[00 - START HERE]]

---

## 📥 Practice sources

**In this vault** — see [README.md](README.md) for the full map:
- **Exam Guide - Sample Questions/** — 12 questions from § 9 of the official exam guide, with Anthropic's own rationales rebutting every distractor. **The highest-authority key here: where it disagrees with a CyberSkill key, it wins** — and that disagreement is itself worth logging below.
- **Vault-authored - Claude Code Scenario Drills/** — 20 questions covering official scenarios 2 and 5 (Claude Code + CI), which no third-party bank tests. **Written for this vault, not drawn from a bank:** topics are exam-accurate, difficulty is uncalibrated, so don't read a score here as a predicted exam score. Log misses tagged `D3`.
- **CyberSkill CCAF - New Mock Exam/** — 60 questions **plus** a matched worked key split by domain. The only complete question+answer pair here; start with this one.
- **CyberSkill CCAF - Mock Exam/** — worked answers only (its question file was never captured), same four domains. A **second sitting of the same bank** as New Mock Exam: 40 of 60 questions coincide, under different numbers.
- **CyberSkill CCAF - Timed Mock 2026-08-24/** — a **third sitting** of that same bank, sat timed on 2026-08-24. **Scored 43/60 (71.67%), one mark below CyberSkill's 72% bar** — which is the site's threshold, not the exam's scaled 720/1000 (see [[Official Exam Blueprint]] § 1). All 60 answers are grader-authoritative; stems captured but not the options, so it drills as open-response only. 48 of 60 coincide with New Mock Exam.
- [[CCA-F-practice-exam-questions]] — an unrelated bank (certificationpractice.com #2564), 60 questions, **no answer key yet**.

> [!WARNING] All four of the sets above number their questions Q1–Q60 and none of the numberings match. Never cross-reference a question by number across sets. (The official set numbers Q1–Q12 and the drill set Q1–Q20 — separate numberings again.)

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

Use the template for misses that have **no** worked key entry. The 17 below already have full write-ups, so they are logged as pointers rather than copied out.

### Logged misses — Timed Mock 2026-08-24 (17 of 60)

Answer keys: [customer_support](CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/Answer%20Key/customer_support.md) · [extraction_pipeline](CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/Answer%20Key/extraction_pipeline.md) · [code_exploration](CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/Answer%20Key/code_exploration.md) · [research_pipeline](CyberSkill%20CCAF%20-%20Timed%20Mock%202026-08-24/Answer%20Key/research_pipeline.md)

| Q | Exam domain | Mine | Right | The rule I missed |
|:--:|---|:--:|:--:|---|
| 13 | D4 | C | **A** | Two derivable values disagree → emit both, flag the delta. Never prompt it away |
| 14 | D4 | C | **D** | Info present but varied in format → few-shot. Don't downgrade the field to optional |
| 18 | D1 | B | **C** | The loop has no decision tree; the `tool_result` is appended and the model reasons |
| 19 | D5 | A | **B** | Regulated advice is out of scope — name the limit, route on |
| 21 | D5 | B | **C** | Separate well-established from contested; don't average incommensurable estimates |
| 23 | D5 | D | **A** | Scope the problem in one question before calling tools |
| 27 | D4 | B | **C** | Batch cadence = arrival wait + 24h + retry headroom ≤ SLA |
| 38 | D5 | B | **A** | Judgment criteria beat counters for semantic conditions |
| 40 | D5 | B | **C** | Context exhaustion → summarize + fresh context, not sharper prompts |
| 41 | D5 | A | **C** | In-session pressure → compact inactive spans, keep the active issue verbatim |
| 42 | D4 | C | **A** | Scarce review capacity → confidence calibrated on a labeled set |
| 47 | D5 | D | **A** | Fix the upstream output contract, not the synthesizer |
| 49 | D5 | C | **A** | Provenance is a structured field, never inline prose |
| 50 | D1 | C | **D** | Delegate goals + quality criteria, never procedures |
| 54 | D1 | B | **C** | Render each content type on its own terms |
| 59 | D4 | C | **B** | Keep the enum closed; add `"other"` + a detail field |
| 60 | D2 | D | **B** | `isError: true` is the error channel — enrich it, don't replace it |

### Misses I could already answer — recognition, not knowledge

Three of the 17 repeat a rule applied **correctly** on a different question in the *same* sitting:

| Missed | Got right, same sitting | The shared rule |
|---|---|---|
| **Q49** — chose inline prose citations | **Q15** — chose structured claim–source mappings | Provenance is a field, never a sentence |
| **Q60** — abandoned `isError` for a `status` field | **Q20** — correctly enriched errors with `errorCategory` / `isRetryable` | Structure goes *alongside* `isError: true`, not instead of it |
| **Q40** — chose "prompt more specifically" | **Q11** and **Q58** — chose subagent, then scratchpad | Context exhaustion → compact / isolate |

**So the remedy is to drill the trigger, not the content.** Under time pressure the recall is there and the *recognition* fails. Two vault errors also contributed and have since been fixed: `D5` taught "escalate immediately, no investigation, non-negotiable" for Q23, and `D4` taught 6-hour batch windows for Q27.

---

## 📊 Weak-area tally

Track which subdomains keep tripping you up. Add a tally mark each time you miss one; the highest counts are what to re-study.

| Domain | Subdomain | Misses | Notes |
|--------|-----------|:------:|-------|
| D1 | Agent loop / `stop_reason` | 1 | T-Q18 — thought the loop branches on a decision tree |
| D1 | Multi-agent / subagents | 2 | T-Q50 procedures-not-goals · T-Q54 flattened all output types |
| D1 | Hooks vs prompts |  |  |
| D2 | Tool interface design |  |  |
| D2 | MCP error responses (`isError`) | 1 | T-Q60 — used a `status` field on a success response |
| D2 | `.mcp.json` vs `~/.claude.json` scoping |  |  |
| D3 | `CLAUDE.md` hierarchy / `@import` |  |  |
| D3 | Plan mode vs direct execution |  |  |
| D3 | CI/CD (`--print`, `--output-format json`) |  |  |
| D4 | JSON schema / `output_config.format` | 2 | T-Q59 enum escape hatch · T-Q14 few-shot for varied layouts |
| D4 | Batch vs sync (`custom_id`) | 1 | T-Q27 — no retry headroom; vault taught 6h, now fixed |
| D4 | Validation & retry loops | 2 | T-Q13 emit both totals · T-Q42 calibrated confidence routing |
| D5 | Context degradation / `/compact` | 2 | T-Q40 · T-Q41 — both were rules I'd applied correctly elsewhere |
| D5 | Escalation triggers | 1 | T-Q38 — chose a failed-call counter over judgment criteria |
| D5 | Provenance / source attribution | 2 | T-Q47 missing dates · T-Q49 inline citations |
| D5 | Scope boundaries / regulated advice | 1 | T-Q19 — offered a legal opinion. Only vault-wide gap the sitting found; now in `00` + Handbook `04` |
| D5 | Scope before tool calls | 1 | T-Q23 — gathered context before knowing the issue |
| D5 | Uncertainty & conflicting findings | 1 | T-Q21 — averaged incommensurable estimates |

---

## 🔁 Recurring traps I keep falling for

> [!NOTE] Part candidate, part confirmed — updated 2026-08-24
> The unmarked rows are **tier-1 patterns** from [[Answer Patterns Index]], restated as *the wrong answer that looks right*. They are the bank's most-punished patterns, not yet proven to be yours.
>
> Rows marked **_(mine)_** are **confirmed against real misses** and carry a tally. The top two were earned in the 2026-08-24 timed sitting.
>
> Tally a mark each time you fall for one. **Delete the rows you never miss** — a trap log only works if every row on it is true about you.

| ❌ The pull I need to resist | ✅ The correct instinct | Tally | Drill |
|---|---|:--:|---|
| **_(mine)_ Picking the option that adds a component** — a calibration layer, a conversion layer, a classification step, an extra context layer, a post-processing normalizer | **Repair the existing contract.** Where two options differ mainly in that one adds infrastructure, the simpler one is usually right. Elaborate machinery is the most common wrong answer in this bank | **5** | T-Q21, T-Q41, T-Q50, T-Q54, T-Q59 |
| **_(mine)_ Prescribing a technique before reading the stem for the stated cause** — reaching for few-shot, or for human review, by reflex | **Diagnose first.** Info present but varied → few-shot · info absent → null · info self-contradictory → emit both and flag · errors in populated fields → calibrated confidence | **4** | T-Q13, T-Q14, T-Q42, T-Q59 |
| Keeping sources **in the prose**, or reconstructing citations at the synthesis step | Claim → source as a **structured, mergeable field** from the very first hop. If rewording can lose it, it will be lost | | Set 1 |
| Picking the trigger that sounds **engineered and measurable** — 3 failed tool calls, a sentiment threshold | Escalate on **reasons**: asked for a human, needs authority the agent lacks, no longer making progress | | Set 2 |
| Choosing the answer that yields a **complete record** — filling the field, picking the closest enum | **Null is an answer.** Partial-with-honest-gaps beats complete-with-invented-values; give closed enums an `"other"` + detail field | | Set 3 |
| Picking the **thorough-sounding** option — read all the files, build an index up front | **Orient → narrow → read.** Structure first, grep second, full reads last. Abstraction before instances | | Set 4 |
| Accepting a design where **workers hand results to each other**, or fan-out nests | Everything flows **through the coordinator's prompts**. Flat fan-out. An arrow between two workers means the wrong diagram | | Set 5 |
| Reaching for a **bigger context window** or "read more carefully" when answers go generic | **Compact, then isolate.** Summarize before you delegate; a scratchpad is durable memory, a bigger window is a slower leak | | Set 6 |
| Mixing up `--continue` / `--resume`, or **starting fresh** when context is still good | `--resume <id>` targets a *specific* session · `fork_session` for mutually-exclusive branches · files changed → resume **and name the delta** | | Set 7 |
| Reaching for **model tier, temperature, or "more examples"** on inconsistent output | "Inconsistent output" is a **missing specification**. Show the canonical shape with *diverse* examples — variety, not volume | | Set 8 |
| _(mine)_ Picking a **prompt-based** answer when the question says "guaranteed" / "compliance" / "cannot rely on the model" | That is **always** code — hook, gate, validator. Prompts move probability; code fixes outcome | | [[00-golden-rules-cheatsheet]] |

Drill-set numbers refer to [[Answer Patterns Index]] § Part 3 — each set is 3–6 questions across the CyberSkill sittings that test the same rule. `T-Q` references point at the 2026-08-24 answer keys and drill as open-response, since that sitting has no options. Work a whole set in one go, then write the rule from memory *before* opening the key.

> [!TIP] The faster tell is the **failure mode**, not the topic
> Nearly every wrong option in this bank fails in one of four ways: a **proxy** standing in for the real signal · a **prompt where code belongs** (or a rule table where judgment belongs) · **information thrown away and then reconstructed** · **complexity that doesn't pay**. Naming the failure mode is quicker than recalling 180 answers. The 2026-08-24 sitting confirmed a **fifth** shape for this list: **an option that adds a component** where the fix is to repair the existing contract. Full catalogue: [[07-anti-patterns-catalog]].

*Related cram material: [[Flashcards]] · [[Critical Terms Glossary]] · Handbook anti-patterns in `04 - Exam Cram/Handbook/`.*

---
tags:
  - CCA-F
  - moc
  - index
date: 2026-07-11
status: active
---

# 🧭 START HERE — CCA-F Vault Map

> [!NOTE] What this vault is
> Study material for the **Claude Certified Architect – Foundations** exam — exam code **CCAR-F**, delivered by **Pearson VUE** (registration via the Anthropic Partner Academy). **60 items · 120 minutes · 4 scenarios drawn at random from a bank of 6.** Passing score **720 / 1000** scaled; multiple-choice + multiple-response; ~6 months practical experience assumed.
> Every exam fact in this vault comes from [[Official Exam Blueprint]] — read it first.

This is the single entry point. Everything is organized into numbered folders by study order.

---

## 🗺️ The path

| #   | Folder             | What's inside                                                                                                             | Start with                                                   |
| --- | ------------------ | ------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| 01  | **Roadmap**        | The official blueprint, the 6-week study plan, resources, progress tracker                                                | [[Official Exam Blueprint]] · [[CCA-F Study Roadmap]]        |
| 02  | **Courses**        | One lesson-doc per required training course — *what to know & memorize after finishing it*                                | See course list ↓                                            |
| 03  | **Domains**        | Deep-dive notes for each of the 5 exam domains + API fundamentals                                                         | [[00 - Claude Model Family & API Fundamentals]]              |
| 04  | **Exam Cram**      | Memorize-by-heart material: golden rules, glossary, flashcards, scenario handbook                                         | [[Flashcards]] · [[Critical Terms Glossary]]                 |
| 05  | **Practice**       | Six exam sets (one folder each) + a deduplicated unified bank + your weak-areas mistake log. 🔒 The three raw CyberSkill sittings and the mistake log are personal and gitignored — start from the Unified Bank                               | [Exam Guide - Sample Questions](05%20-%20Practice/Exam%20Guide%20-%20Sample%20Questions/README.md) **(official — start here)** · [Unified Bank](05%20-%20Practice/CyberSkill%20CCAF%20-%20Unified%20Bank/README.md) · [[Weak Areas Deep Dive]] |
| 06  | **Youtube Course** | Per-episode study guides + flashcard decks from the Peace Of Code course, one folder per episode, mapped to the 5 domains | [[EP01 - Agentic Loops & stop_reason]]                       |

---

## 📚 The 5 exam domains

| # | Domain | Weight | Covers |
| --- | --- | --- | --- |
| 1 | [[D1 - Agentic Architecture & Orchestration]] | **27%** | agent loop, multi-agent, subagents, hooks, sessions |
| 2 | [[D2 - Tool Design & MCP Integration]] | **18%** | tool interfaces, error responses, MCP servers, built-in tools |
| 3 | [[D3 - Claude Code Configuration & Workflows]] | **20%** | `CLAUDE.md` hierarchy, commands/skills, plan mode, CI/CD |
| 4 | [[D4 - Prompt Engineering & Structured Output]] | **20%** | explicit criteria, few-shot, JSON schemas, batch |
| 5 | [[D5 - Context Management & Reliability]] | **15%** | long context, escalation, error propagation, provenance |

> [!TIP] Where the marks are
> **D1 + D3 = 47%** of the exam. D5 is the lightest at 15%. The 30 task statements behind these weights are listed in [[Official Exam Blueprint]].

---

## 🎓 Required courses (folder 02)

Take these in roughly this order; each course note captures the exam-relevant takeaways.

- [[C1 - Claude 101]] — foundations & prompting basics
- [[C2 - Claude Platform 101]] — the API, models, agent loop
- [[C3 - Introduction to MCP]] — Model Context Protocol → Domain 2
- [[C4 - Claude Code 101]] — Claude Code config & workflows → Domain 3
- [[C5 - Introduction to Subagents]] — multi-agent orchestration → Domain 1
- [[C6 - Building with the Claude API]] — tool use, structured output, batch → Domains 4 & 2

---

## 🎥 YouTube Course companion (folder 06)

Peace Of Code's full-course episodes, one folder per episode (`06 - Youtube Course/EP<NN> - <Title>/`), each with a study guide + its own flashcard deck. Mapped to the 5 domains; ✅ = written, ⬜ = folder pre-created, not yet authored.

**Domain 1 — Agentic Architecture & Orchestration**
- ✅ [[EP01 - Agentic Loops & stop_reason]]
- ✅ [[EP02 - Multi-Agent Systems & Coordinator Patterns]]
- ✅ [[EP03 - Subagent Context Passing & Session Management]]
- ✅ [[EP04 - Multi-Agent System in Python (Claude SDK)]]
- ✅ [[EP05 - PreToolUse, PostToolUse Hooks & Task Decomposition]]

**Domain 2 — Tool Design & MCP Integration**
- ✅ [[EP06 - Tool Descriptions & Tool Misrouting]]
- ✅ [[EP07 - Agent Error Handling & tool_choice]]
- ✅ [[EP08 - MCP Servers, Config & Cline]]
- ✅ [[EP09 - Claude Built-in Tools]]

**Domain 3 — Claude Code Configuration & Workflows**
- ✅ [[EP10 - CLAUDE.md Hierarchy & Config Rules]]
- ✅ [[EP11 - Custom Slash Commands & Skills]]
- ✅ [[EP12 - Plan Mode vs Execute]]
- ✅ [[EP13 - Claude Code CI-CD Pipelines]]

**Domain 4 — Prompt Engineering & Structured Output**
- ✅ [[EP14 - Prompt Engineering - Explicit Criteria & False Positives]]
- ✅ [[EP15 - Few-Shot Prompting]]
- ✅ [[EP16 - Structured Output & JSON Schema]]
- ✅ [[EP17 - Batch API & Multi-Pass Review]]

**Domain 5 — Context Management & Reliability**
- ✅ [[EP18 - Why AI Agents Forget (Context Engineering)]]
- ✅ [[EP19 - Subagent Error Propagation & Context Management]]
- ✅ [[EP20 - When AI Needs a Human]]

**Bonus (cross-domain)**
- ✅ [[Bonus - Exam Questions Solved & Exam Traps]]

> [!TIP] Source transcripts
> Raw captions live in `youtube-transcript/` — read-only input, never study content. Generate new episode notes with `/cca-episode <transcript-path>` (run from inside `CCA-F/`).

---

## ⭐ The one rule that answers ~30% of the exam

> [!IMPORTANT] Code vs. Prompt
> If something **MUST always happen** → enforce it in **CODE** (hook / gate / schema validation / deterministic routing).
> If something should **usually** be right → guide it with a **PROMPT** (instructions / examples / tool descriptions).
> Prompts shift *probability*; code guarantees *outcome*. Any question with "compliance", "guaranteed", "100%", or "cannot be left to model discretion" → **code/hook** answer.

---

## 🔖 The night before

Re-read only: [[00-golden-rules-cheatsheet|Golden Rules]] (in `04 - Exam Cram/Handbook/`), [[Critical Terms Glossary]], and [[Flashcards]] — starting with its **Exam Blueprint** section, which carries the weights, the six scenarios, the scoring model, and the out-of-scope list in card form.

*Working conventions for this vault live in `AGENTS.md` (vault root).*
